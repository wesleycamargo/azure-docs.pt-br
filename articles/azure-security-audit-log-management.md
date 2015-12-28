<properties
   pageTitle="Gerenciamento de log de segurança e de auditoria do Microsoft Azure | Microsoft Azure"
   description="O artigo oferece uma introdução à geração, coleta e análise de logs de segurança de serviços hospedados no Azure. Destina-se a profissionais de TI e a analistas de segurança que lidam diariamente com o gerenciamento de ativos de informação, incluindo as pessoas responsáveis por iniciativas de segurança e de conformidade da organização."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Gerenciamento de log de segurança e de auditoria do Microsoft Azure

O Azure permite que os clientes gerem e coletem eventos de segurança das funções IaaS (Infraestrutura como Serviço) e PaaS (Plataforma como Serviço) do Azure para armazenamento central nas assinaturas deles. Então, os clientes poderão usar o [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/) para agregar e analisar os eventos coletados. Além disso, esses eventos coletados podem ser exportados para sistemas SIEM (gerenciamento de informações e eventos de segurança) locais com o objetivo de realizar um monitoramento contínuo.

O ciclo de vida de registro em log, de análise e de monitoramento de segurança do Azure inclui:

- **Geração**: instrumentação de aplicativos e da infraestrutura para gerar eventos
- **Coleta**: configuração do Azure a fim de coletar os diversos logs de segurança em uma conta de armazenamento
- **Análise**: use ferramentas do Azure como o HDInsight e sistemas SIEM locais para analisar os logs e gerar informações de segurança
- **Monitoramento e geração de relatórios**: o Azure oferece sistemas centralizados de monitoramento e de análise que oferecem visibilidade contínua e alertas em tempo hábil

Este artigo destaca as fases de coleta e de geração do ciclo de vida.

## Geração de log
Os eventos de segurança são gerados no Log de Eventos do Windows para os canais **Sistema**, **Segurança** e **Aplicativo** em máquinas virtuais. Para garantir que os eventos sejam registrados sem potencial perda de dados, será importante configurar corretamente o tamanho do log de eventos. Baseie o tamanho do log de eventos no número de eventos gerados pelas configurações de política de auditoria e pelas políticas de coleta de eventos definidas. Para saber mais, consulte [Planejamento de monitoramento e gerenciamento de auditoria de segurança](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4).

>[AZURE.NOTE]Ao usar o Encaminhamento de Eventos do Windows (WEF) ou o Diagnóstico do Azure (explicado na seção [Coleta de log](#log-collection)) para extrair os logs dos Serviços de Nuvem ou das máquinas virtuais, considere o potencial impacto de interrupções no sistema. Por exemplo, se o ambiente do WEF ficar inativo por algum tempo, verifique se o log é grande o suficiente para acomodar um período mais longo, ou prepare-se para possíveis perdas de dados.

Há um conjunto de eventos de segurança do sistema operacional habilitado por padrão para os aplicativos de Serviços de Nuvem implantados no Azure, e para máquinas virtuais criadas a partir do [Marketplace de Máquinas Virtuais do Azure](http://azure.microsoft.com/marketplace/virtual-machines/#microsoft). Os clientes podem adicionar, remover ou modificar os eventos para auditoria por meio da personalização da política de auditoria do sistema operacional. Para saber mais, consulte [Referência sobre as configurações da política de segurança](http://technet.microsoft.com/library/jj852210.aspx).

Use os métodos a seguir para gerar logs adicionais do sistema operacional (por exemplo, mudanças na política de auditoria) e de componentes do Windows (por exemplo, o IIS):

- Política de Grupo para distribuição de configurações de política para máquinas virtuais no Azure que tiverem ingressado no domínio
- Configuração de Estado Desejado (DSC) para aplicar e gerenciar as configurações de política. Para saber mais, consulte [DSC do Azure PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx).
- Código de inicialização de função de Implantação de Serviço para implantar configurações de Serviços de Nuvem (cenário de PaaS)

A configuração de tarefas de inicialização de função do Azure permite a execução do código antes do início de uma função. Você pode definir uma tarefa de inicialização de uma função adicionando o elemento **Inicialização** à definição da função no arquivo de definição de serviço, como mostra o exemplo a seguir. Para obter mais informações, consulte [Executar Tarefas de Inicialização no Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

O arquivo de tarefa que deve ser executado como uma tarefa de Inicialização (EnableLogOnAudit.cmd no exemplo a seguir) precisa ser incluído em seu pacote de compilação. Se você estiver usando o Visual Studio, adicione o arquivo ao seu projeto de nuvem, clique com o botão direito do mouse no nome do arquivo, clique em **Propriedades** e defina **Copiar para Diretório de Saída** como **Copiar sempre**.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Conteúdo de EnableLogOnAudit.cmd:

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

[Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx), usada no exemplo anterior, é uma ferramenta de linha de comando incluída no sistema operacional Windows Server que permite o gerenciamento das configurações de política de auditoria.

Além da geração de logs de eventos do Windows, é possível configurar vários componentes do sistema operacional Windows para gerar logs importantes para o monitoramento e a análise de segurança. Por exemplo, os logs do Serviços de Informações da Internet (IIS) e os logs http.err são gerados automaticamente para funções Web, e podem ser configurados para coleta. Esses logs fornecem informações valiosas que podem ser usadas para identificar ataques contra a função Web ou acesso não autorizado. Para saber mais, consulte [Configurar o registro em log no IIS](http://technet.microsoft.com/library/hh831775.aspx) e [Registro em log avançado do IIS – registro em log personalizado](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging).

Para alterar o registro em log do IIS em uma função Web, os clientes podem adicionar uma tarefa de inicialização ao arquivo de definição de serviço da função Web. O exemplo a seguir habilita o registro em log de HTTP para um site chamado Contoso, e especifica que o IIS deve registrar todas as solicitações para o site Contoso.

A tarefa que atualiza a configuração do IIS deve ser incluída no arquivo de definição de serviço da função Web. As alterações a seguir no arquivo de definição de serviço executam uma tarefa de inicialização que configura o registro em log do IIS por meio da execução de um script chamado ConfigureIISLogging.cmd.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Conteúdo de ConfigureIISLogging:cmd

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>Coleta de logs
A coleta de logs e de eventos de segurança dos Serviços de Nuvem ou das máquinas virtuais no Azure ocorre por meio de dois métodos principais:

- Diagnóstico do Azure, coleta eventos na conta de armazenamento do Azure do cliente
- Encaminhamento e Eventos do Windows (WEF), uma tecnologia em computadores que executam Windows

Veja na tabela abaixo algumas das principais diferenças entre essas duas tecnologias. Com base em seus requisitos e nessas diferenças principais, o método apropriado deve ser escolhido para implementação da coleta de logs.

| Diagnóstico do Azure | Encaminhamento de Eventos do Windows |
|-----|-----|
|Oferece suporte aos Serviços de Nuvem do Azure e às Máquinas Virtuais do Azure | Oferece suporte apenas a Máquinas Virtuais do Azure ingressadas no domínio |
|Oferece suporte a vários formatos de log, como logs de eventos do Windows, rastreamentos do [Rastreamento de Eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) e logs do IIS. Para saber mais, consulte [Fontes de dados com suporte do Diagnóstico do Azure](#diagnostics) |Oferece suporte apenas aos logs de eventos do Windows |
|Envia os dados coletados ao Armazenamento do Azure |Move os dados coletados para servidores de coleta centrais |

##	Coleta de dados de eventos de segurança com o Encaminhamento de Eventos do Windows
No caso das Máquinas Virtuais do Azure ingressadas em um domínio, você pode configurar o WEF usando as mesmas configurações de Política de Grupo usadas para computadores que ingressaram em um domínio local. Para saber mais, consulte [Nuvem híbrida](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx).

Com essa abordagem, uma organização pode adquirir uma assinatura do IaaS, conectá-la à rede corporativa usando a [Rota Expressa](http://azure.microsoft.com/services/expressroute/) ou a VPN site a site e, em seguida, ingressar as máquinas virtuais que você tem no Azure no domínio corporativo. Posteriormente, você poderá configurar o WEF nas máquinas ingressadas no domínio.

O encaminhamento de eventos é dividido em duas partes: a origem e o coletor. A origem é o computador no qual os logs de segurança são gerados. O coletor é o servidor centralizado que coleta e consolida os logs de eventos. Os administradores de TI podem assinar eventos para que possam receber e armazenar eventos encaminhados de computadores remotos (a origem do evento). Para saber mais, consulte [Configurar computadores para encaminhar e coletar eventos](http://technet.microsoft.com/library/cc748890.aspx).

Os eventos do Windows coletados podem ser enviados para ferramentas de análise locais, como um SIEM, para outras análises.

## Coleta de dados de segurança com o Diagnóstico do Azure
O Diagnóstico do Azure permite a coleta de dados de diagnóstico de uma função de trabalho ou função Web do serviço de nuvem ou de máquinas virtuais em execução no Azure. É uma extensão de agente convidado predefinida que precisa ser habilitada e configurada para coleta de dados. A assinatura do cliente pode incluir o envio dos dados ao Armazenamento do Azure.

Os dados são criptografados em trânsito (usando HTTPS). Os exemplos fornecidos neste documento estão usando o Diagnóstico do Azure 1.2. Recomendamos a atualização para a versão 1.2 ou superior para coleta de dados de segurança. Para saber mais, consulte [Coletar dados do log usando o Diagnóstico do Azure](http://msdn.microsoft.com/library/gg433048.aspx)

O diagrama a seguir mostra um fluxo de dados de alto nível para a coleta de dados de segurança que usa o Diagnóstico do Azure e outras análises e monitoramento.

![][1]

O Diagnóstico do Azure move os logs de aplicativos de Serviços de Nuvem clientes e de [Máquinas Virtuais do Azure](virtual-machines-about.md) no Armazenamento do Azure. Com base em um formato de log, alguns dados são armazenados em tabelas do Azure e em alguns blobs. Os dados coletados no [Armazenamento do Azure](storage-introduction.md) podem ser baixados em sistemas SIEM locais usando a biblioteca de clientes do Armazenamento do Azure para monitoramento e análise.

Além disso, o HDInsight pode ser usado para realizar outras análises dos dados na nuvem. Veja a seguir alguns exemplos de coleta de dados de segurança que usam o Diagnóstico do Azure.

### Coleta de dados de segurança de Máquinas Virtuais do Azure usando o Diagnóstico do Azure

Os exemplos a seguir usam cmdlets do Diagnóstico do Azure 1.2 e do Azure PowerShell para habilitar a coleta de dados de segurança das máquinas virtuais. Os dados são coletados das máquinas virtuais em um intervalo programado (ou seja, configurável) e enviados para o Armazenamento do Azure na assinatura do cliente. Nesta seção, abordaremos dois cenários de coleta de logs usando o Diagnóstico do Azure:

1. Configuração de uma nova instância de um pipeline de coleta de logs de segurança em uma máquina virtual.
2. Atualização de um pipeline de coleta de logs de segurança existente com uma nova configuração em uma máquina virtual.

#### Configuração de uma nova instância de um pipeline de coleta de logs de segurança em uma máquina virtual
Neste exemplo, criamos uma nova instância de um pipeline de coleta de logs de segurança que usa o Diagnóstico do Azure e detectamos eventos de falha de logon (IDs de evento 4624 e 4625) das máquinas virtuais. Você pode implementar as seguintes etapas em seu ambiente de desenvolvimento, ou você pode usar uma sessão de Área de trabalho remota por meio do Protocolo RDP para o nó na nuvem.

##### Etapa 1: Instalar o SDK do Azure PowerShell
O SDK do Azure PowerShell fornece cmdlets para configuração do Diagnóstico do Azure em Máquinas Virtuais do Azure. Os cmdlets necessários estão disponíveis no Azure PowerShell versão 0.8.7 ou posterior. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

##### Etapa 2: Preparar o arquivo de configuração
Prepare o arquivo de configuração com base nos eventos que você deseja coletar. A seguir, um exemplo de um arquivo de configuração do Diagnóstico do Azure para coletar eventos do Windows do canal **Segurança**, com a adição de filtros para coletar somente eventos de êxito e de falha de logon. Para saber mais, consulte [Esquema de configuração do Diagnóstico do Azure 1.2](http://msdn.microsoft.com/library/azure/dn782207.aspx).

A conta de armazenamento pode ser especificada no arquivo de configuração ou como um parâmetro ao executar os cmdlets do Azure PowerShell para configurar o Diagnóstico do Azure.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

Ao salvar o conteúdo anterior como um arquivo XML, defina a codificação como **UTF-8**. Se você estiver usando o Bloco de Notas, verá a opção de codificação disponível na caixa de diálogo "Salvar como". A tabela a seguir lista alguns atributos importantes do arquivo de configuração.

| Atributo | Descrição |
|----- |-----|
| overallQuotaInMB | A quantidade máxima de espaço no disco local que pode ser consumido pelo Diagnóstico do Azure (o valor é configurável). |
| scheduledTransferPeriod | O intervalo entre transferências agendadas para o Armazenamento do Azure, arredondado para o minuto mais próximo. |
| Nome | Em WindowsEventLog, esse atributo é a consulta XPath que descreve os eventos do Windows que devem ser coletados. Você pode filtrar a coleta de dados adicionando um filtro, por exemplo, ID do Evento, Nome do Provedor ou Canal. |

Todos os dados do Log de Eventos do Windows são transferidos para uma tabela chamada **WADWindowsEventLogsTable**. Atualmente, o Diagnóstico do Azure não oferece suporte à renomeação da tabela.

##### <a name="step3"></a> Etapa 3: Validar o arquivo XML de configuração
Use o procedimento a seguir para confirmar que não existe um erro no arquivo XML de configuração e que ele é compatível com o esquema do Diagnóstico do Azure:

1. Para baixar o arquivo de esquema, execute o comando a seguir e salve o arquivo.

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. Depois de baixar o arquivo de esquema, você poderá validar o arquivo XML de configuração no esquema. Para validar o arquivo usando o Visual Studio:
  - Abra o arquivo XML no Visual Studio
  - Pressione F4 para abrir **Propriedades**
  - Clique em **Esquema**, clique em **Adicionar**, selecione o arquivo de esquema que você baixou (WadConfigSchema.XSD) e clique em **OK**

3.	No menu **Exibir**, clique em **Lista de Erros** para ver se há erros de validação.

##### <a name="step4"></a> Etapa 4: Configurar o Diagnóstico do Azure
 Use as etapas a seguir para habilitar o Diagnóstico do Azure e iniciar a coleta de dados:

 1.	Para abrir o Azure PowerShell, digite **Add-AzureAccount** e pressione ENTER.
 2.	Faça logon usando sua conta do Azure.
 3.	Execute o script do PowerShell a seguir. Atualize storage\_name, key, config\_path, service\_name e vm\_name.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### Etapa 5: Gerar eventos
Para fins de demonstração, criaremos alguns eventos de logon e verificaremos se os dados estão fluindo para o Armazenamento do Azure. Como mostramos na Etapa 2, o arquivo XML é configurado para coletar a ID de Evento 4624 (Logon Bem-sucedido) e a ID de Evento 4625 (Falha de Logon) do canal **Segurança**.

 Para gerar estes eventos:

1.	Abra uma sessão RDP para sua máquina virtual.
2.	Insira as credenciais incorretas para gerar alguns eventos de falha de logon (ID de Evento 4625).
3.	Após algumas tentativas de logon com falha, insira as credenciais corretas para gerar um evento de logon bem-sucedido (ID de Evento 4624).

##### Etapa 6: Exibir os dados
Cinco minutos depois de concluir as etapas anteriores, os dados deverão começar a fluir para a conta de armazenamento do cliente com base na configuração no arquivo XML. Há muitas ferramentas disponíveis para exibir os dados do Armazenamento do Azure. Para obter mais informações, consulte:

- [Procurando recursos de armazenamento com o Gerenciador de Servidores (a página pode estar em inglês)](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Gerenciador de Armazenamento do Azure 6 Visualização 3 (agosto de 2014)](http://azurestorageexplorer.codeplex.com/)

Para exibir seus dados:

1.	No Visual Studio (2013, 2012 e 2010 com SP1), clique em **Exibir** e clique em **Gerenciador de Servidores**.
2.	Navegue até a conta de armazenamento.
3.	Clique em **Tabelas** e clique duas vezes nas tabelas apropriadas para exibir os logs de segurança coletados de máquinas virtuais. ![][2]

4.	Clique com o botão direito do mouse na tabela chamada WADWindowsEventLogsTable e clique em **Exibir Dados** para abrir o modo de exibição de tabela, como mostrado aqui:

![][3]

Na tabela anterior de armazenamento, **PartitionKey**, **RowKey** e **Timestamp** são propriedades do sistema.

- **PartitionKey** é um carimbo de hora em segundos e é um identificador exclusivo para a partição na tabela.
- **RowKey** é um identificador exclusivo de uma entidade em uma partição.

Juntas, **PartitionKey** e **RowKey** identificam exclusivamente cada entidade dentro de uma tabela.

- Timestamp é um valor de data/hora mantido no servidor para controlar quando uma entidade foi modificada pela última vez.

>[AZURE.NOTE]O tamanho máximo de linha em uma tabela do Armazenamento do Azure é limitado a 1 MB. Uma conta de armazenamento pode conter até 200 TB de dados de blobs, filas e tabelas se a conta tiver sido criada depois de junho de 2012. Portanto, o tamanho da tabela pode aumentar até 200 TB se os blobs e as filas não ocuparem qualquer espaço de armazenamento. As contas criadas antes de junho de 2012 têm um limite de 100 TB.

O Gerenciador de Armazenamento também oferece a opção para editar dados de tabela. Clique duas vezes em uma linha específica no modo de exibição de Tabela para abrir a janela Editar Entidade, como mostrado aqui:

![][4]

#### Atualizar um pipeline de coleta de logs de segurança existente com uma nova configuração em uma máquina virtual
Nesta seção, atualizamos um pipeline de coleta de logs de segurança existente do Diagnóstico do Azure em uma máquina virtual de detectamos erros no log de eventos de aplicativo do Windows.

##### Etapa 1: Atualizar o arquivo de configuração para incluir eventos de seu interesse
O arquivo do Diagnóstico do Azure criado no exemplo anterior precisa ser atualizado para incluir os tipos de erro do log de eventos de aplicativo do Windows.

>[AZURE.NOTE]Quaisquer definições de configuração existentes do Diagnóstico do Azure precisam ser mescladas ao novo arquivo de configuração. As configurações definidas no novo arquivo substituirão as configurações existentes.

Para recuperar a definição de configuração existente, use o cmdlet **Get-AzureVMDiagnosticsExtension**. A seguir, um exemplo de script do Azure PowerShell para recuperar a configuração existente:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
Atualize a configuração do Diagnóstico do Azure para coletar erros de log do evento do aplicativo e eventos críticos do Windows como a seguir:

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Valide o arquivo de configuração usando as mesmas etapas exibidas na [Etapa 3: Validar o arquivo XML de configuração](#step3).

##### Etapa 2: Atualizar o Diagnóstico do Azure para usar o novo arquivo de configuração
Use os cmdlets **Set-AzureVMDiagnosticsExtension** and **Update-AzureVM** para atualizar a configuração, como exibido na [Etapa 4: Configurar o Diagnóstico do Azure](#step4).

##### Etapa 3: Verificar as definições de configuração
Execute o comando a seguir para verificar se as configurações foram atualizadas:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### Etapa 4: Gerar eventos
Para este exemplo, execute o seguinte comando a fim de gerar um log de eventos do aplicativo do tipo **Erro**:

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

Abra o Visualizador de Eventos para verificar se o evento foi criado.

![][6]

##### Etapa 5: Exibir os dados
Abra o Gerenciador de Servidores no Visual Studio para exibir os dados do log. Você deverá ver uma **ID de Evento 100** criada em **ContosoDesktop**, como mostrado aqui:

![][7]

## Coleta de dados de segurança dos Serviços de Nuvem do Azure usando o Diagnóstico do Azure

Agora, usaremos o Diagnóstico do Azure para explorar os dois cenários de coleta de logs dos Serviços de Nuvem do Azure, assim como na seção anterior sobre Máquinas Virtuais (IaaS):

1.	Configure uma nova instância do pipeline de log de segurança em um serviço de nuvem.
2.	Atualize um pipeline de coleta de logs existente com uma nova configuração em um serviço de nuvem.

O guia passo a passo desta seção inclui:

1.	Compilar um serviço de nuvem.
2.	Configurar o serviço de nuvem para coleta de logs de segurança usando o Diagnóstico do Azure.
3.	Ilustre a geração e coleta de eventos de segurança no Serviço de Nuvem:

    - Adicionar um administrador a um grupo local com uma elevação de privilégio
    - Criação de novo processo
4.	Atualize um pipeline de coleta de logs existente em um serviço de nuvem:

    - Habilitar a auditoria de eventos de firewall de host (como um exemplo de eventos de segurança de rede) usando Auditpol
    - Configure dados de auditoria de firewall que serão coletados e mostre os eventos coletados na conta de armazenamento do cliente
5.	Mostre a distribuição de eventos de segurança do Windows e a detecção de pico.
6.	Configure a coleta de logs do IIS e verifique os dados.

Todos os eventos e os logs são coletados em uma conta de armazenamento do cliente no Azure. Os eventos podem ser exibidos e exportados pelo cliente para sistemas SIEM locais. Eles também podem ser agregados e analisados usando HDInsight.

### Configuração de uma nova instância de um pipeline de coleta de logs em um serviço de nuvem
Neste exemplo, criamos uma nova instância de um pipeline de coleta de logs de segurança que usa o Diagnóstico do Azure e detectamos uma adição de usuário a um grupo local e eventos de criação de novo processo em uma instância de serviço de nuvem.

#### Etapa 1: Criar um serviço de nuvem (função Web) e implantá-lo

1.	No computador do desenvolvedor, inicie o Visual Studio 2013.
2.	Crie um novo projeto de serviço de nuvem (nosso exemplo usa ContosoWebRole).
3.	Selecione a função Web **ASP.NET**.
4.	Selecione o projeto **MVC**.
5.	No Gerenciador de Soluções, clique em **Funções** e clique duas vezes na função Web (WebRole1) para abrir a janela **Propriedades**.
6.	Na guia **Configuração**, desmarque a caixa de seleção **Habilitar o Diagnóstico** para desabilitar a versão do Diagnóstico do Azure que é fornecida com o Visual Studio 2013. ![][8]

7.	Compile sua solução para verificar que você não tem erros.
8.	Abra o arquivo WebRole1/Controllers/HomeController.cs.
9.	Adicione o seguinte método para permitir que o exemplo de aplicativo registre em log o código de status HTTP 500 como um exemplo de evento de log do IIS (isso será usado no exemplo sobre IIS posteriormente):

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 Clique com o botão direito do mouse no projeto do serviço de nuvem e clique em **Publicar**.

#### Etapa 2: Preparar o arquivo de configuração
Agora, prepararemos o arquivo de configuração do Diagnóstico do Azure para adicionar os eventos que podem ajudar a detectar as seguintes situações:

- Nova adição do usuário a um grupo local
- Criação de novo processo

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### Etapa 3: Validar o arquivo XML de configuração
Valide o arquivo de configuração usando as mesmas etapas exibidas na [Etapa 3: Validar o arquivo XML de configuração](#step3).
#### Etapa 4: Configurar o Diagnóstico do Azure
Execute o script do Azure PowerShell a seguir para habilitar o Diagnóstico do Azure (atualize storage\_name, key, config\_path e service\_name).

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para verificar se o serviço tem a configuração de diagnóstico mais recente, execute o seguinte comando do Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Etapa 5: Gerar eventos
Para gerar eventos:

1.	Para iniciar uma sessão de Área de trabalho remota para sua instância de serviço de nuvem, no Visual Studio, abra o Gerenciador de Servidores, clique com o botão direito do mouse na instância de função e clique em Conectar usando a Área de trabalho remota.
2.	Abra um prompt de comando elevado e execute os seguintes comandos para criar uma conta de administrador local na máquina virtual:


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	Abra o Visualizador de Eventos, abra o canal **Segurança** e observe que um evento 4732 foi criado, conforme exibido aqui:

![][9]

#### Etapa 6: Exibir os dados
Aguarde cerca de cinco minutos até que o agente do Diagnóstico do Azure envie os eventos para a tabela de armazenamento.

![][10]

Para validar o evento de Criação de Processo, abra um Bloco de Notas. Veja que um evento de Criação do Processo foi registrado no canal Segurança.

![][11]

Agora você pode exibir o mesmo evento em sua conta de armazenamento, como aqui:

![][12]

### Atualizar um pipeline de coleta de logs existente com uma nova configuração em um serviço de nuvem
Nesta seção, atualizamos um pipeline de coleta de logs de segurança existente do Diagnóstico do Azure e detectamos Eventos de mudança no Firewall do Windows em uma instância do Serviço de Nuvem.

Para detectar mudanças no firewall, atualizaremos a configuração existente para incluir eventos de mudança no firewall.

#### Etapa 1: Obter a configuração existente
>[AZURE.NOTE]As novas definições de configuração substituirão a configuração existente. Portanto, é importante que as definições de configuração existentes do Diagnóstico do Azure sejam mescladas ao novo arquivo de configuração.

Para recuperar a definição de configuração existente, você pode usar o cmdlet **Get-AzureServiceDiagnosticsExtension**:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Etapa 2: Atualizar a configuração XML para incluir eventos do firewall

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Valide o conteúdo XML usando o mesmo processo de validação descrito na [Etapa 3: Validar o arquivo XML de configuração](#step3).

#### Etapa 3: Atualizar o Diagnóstico do Azure para usar a nova configuração

Execute o script do Azure PowerShell a seguir para atualizar o Diagnóstico do Azure a fim de usar a nova configuração (atualize storage\_name, key, config\_path e service\_name com as informações de seu serviço de nuvem).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para verificar se o serviço tem a configuração de diagnóstico mais recente, execute o seguinte comando do Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Etapa 4: Habilitar eventos do firewall

1.	Abra uma sessão de Área de Trabalho Remota para sua instância de serviço de nuvem.
2.	Abra um prompt de comando elevado e execute o seguinte comando:

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### Etapa 5: Gerar eventos

1.	Abra o Firewall do Windows e clique em **Regras de Entrada**.
2.	Clique em **Adicionar Nova Regra** e clique em **Porta**.
3.	No campo **Portas Locais**, digite **5000** e clique três vezes em **Avançar**.
4.	No campo **Nome**, digite **Test5000** e clique em **Concluir**.
5.	Abra o Visualizador de Eventos, abra o canal **Segurança** e observe que uma ID de evento 4946 foi criada, conforme exibido aqui:

![][13]

#### Etapa 6: Exibir os dados
Aguarde cerca de cinco minutos para que o agente do Diagnóstico do Azure transfira os dados do evento para a tabela de armazenamento.

![][14]

### Detecção de pico e distribuição de eventos de segurança
Quando os eventos estiverem na conta de armazenamento do cliente, os aplicativos poderão usar as bibliotecas de cliente de armazenamento para acessar e executar a agregação de eventos. Para obter o exemplo de código e acessar os dados da tabela, consulte [Como recuperar dados da tabela](storage-dotnet-how-to-use-tables.md).

Veja a seguir um exemplo de agregação de evento: Quaisquer picos na distribuição de eventos poderão ser investigados em busca de atividades anormais.

![][15]

### Coleta e processamento de log do IIS usando o HDInsight
Nesta seção, coletamos os logs do IIS de sua instância de função Web e movemos os logs para um blob do Azure na conta de armazenamento do cliente.

#### Etapa 1: Atualizar o arquivo de configuração para incluir a coleta de logs do IIS

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Na configuração anterior do Diagnóstico do Azure, **containerName** é o nome do contêiner de blob ao qual os logs serão movidos dentro da conta de armazenamento do cliente.

Valide o arquivo de configuração usando as mesmas etapas exibidas na [Etapa 3: Validar o arquivo XML de configuração](#step3).


#### Etapa 2: Atualizar o Diagnóstico do Azure para usar a nova configuração
Execute o script do Azure PowerShell a seguir para atualizar o Diagnóstico do Azure a fim de usar a nova configuração (atualize storage\_name, key, config\_path e service\_name com as informações de seu serviço de nuvem).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para verificar se o serviço tem a configuração de diagnóstico mais recente, execute o seguinte comando do Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Etapa 3: Gerar logs do IIS

1.	Abra um navegador da Web e navegue até a função Web do serviço de nuvem (por exemplo, http://contosowebrole.cloudapp.net/).
2.	Navegue até as páginas **Sobre** e **Contato** para criar alguns eventos de log.
3.	Navegue até uma página que gera um código de status 500 (por exemplo, http://contosowebrole.cloudapp.net/Home/StatusCode500). Você verá um erro como o seguinte. Lembre-se de que adicionamos o código para **StatusCode500** na Etapa 1 da seção Configurar nova instância do pipeline de coleta de logs em um ServiceName de nuvem. ![][16]
4.	Abra uma sessão de Área de Trabalho Remota para sua instância de serviço de nuvem.
5.	Abra o Gerenciador do IIS.
6.	O Registro em Log do IIS está habilitado por padrão e está definido para gerar os arquivos que contêm todos os campos no formato W3C por hora. Clique em **Procurar** e haverá pelo menos um arquivo de log, como mostrado aqui: ![][17]

7.	Aguarde cerca de cinco minutos para que o agente do Diagnóstico do Azure envie por push o arquivo de log para o contêiner de blob. Para validar os dados, abra o **Gerenciador de Servidores** > **Armazenamento** > **Conta de Armazenamento** > **Blobs**. Como mostrado aqui, o blob **iislogs** é criado: ![][18]

8.	Clique com o botão direito do mouse e selecione **Exibir Contêiner de Blob** para exibir o arquivo de log do IIS armazenado no blob: ![][19]
9.	Quando os eventos do IIS estiverem na conta de armazenamento do cliente, os aplicativos que aproveitam a análise de HDInsight podem ser usados para executar a agregação de eventos. O gráfico de linha a seguir é um exemplo de uma tarefa de agregação de eventos que mostra o Código de Status HTTP 500: ![][20]

## Recomendações de coleta de logs de segurança
Durante a coleta de logs de segurança, recomendamos que você:

- Colete seus próprios eventos de log de auditoria específicos ao aplicativo de serviço.
- Configure somente os dados que você precisa para análise e monitoramento. A captura de dados demais pode dificultar a solução de problemas e pode afetar os custos de armazenamento ou de serviço.
- Mescle as definições de configuração existentes do Diagnóstico do Azure com as alterações feitas. O novo arquivo de configuração substitui a configuração existente.
- Escolha o intervalo **Período de Transferência Agendado** com sabedoria. Períodos de transferência mais curtos aumentarão a relevância dos dados, mas isso pode aumentar a sobrecarga de processamento e os custos de armazenamento.

>[AZURE.NOTE]A outra variável que afetará consideravelmente a quantidade de dados coletados é o nível de registro em log. Este é um exemplo de como filtrar logs por nível de registro em log:

    System!*[System[(Level =2)]]

O nível de registro log é cumulativo. Se o filtro for definido como **Aviso**, os eventos de **Erro** e **Crítico** também serão coletados.

- Limpe periodicamente os dados de diagnóstico do Armazenamento do Azure se eles não forem mais necessários.

>[AZURE.NOTE]Para saber mais sobre os dados de diagnóstico, consulte [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx). Os contêineres e tabelas que armazenam dados de diagnóstico são como outros contêineres e tabelas, é possível excluir blobs e entidades da mesma maneira que você faria com outros dados. É possível excluir os dados de diagnóstico programaticamente por meio de uma das bibliotecas de cliente de armazenamento, ou visualmente por meio de um [cliente do gerenciador de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

- É uma prática recomendada armazenar dados de serviço e dados de log de segurança em contas de armazenamento separadas. Esse isolamento garante que a gravação dos dados do log de segurança não afetará o desempenho de armazenamento de dados de serviço de produção.
- Escolha a duração de retenção de log com base na política de conformidade e nos requisitos de análise e monitoramento de dados de sua organização.

## Exportando os logs de segurança para outro sistema
Você pode baixar os dados de blob usando a Biblioteca de Cliente do Armazenamento do Azure e exportá-lo para o sistema local para processamento. Para obter um exemplo de código a fim de gerenciar os dados de blob, consulte [Como usar o armazenamento de blob no .NET](storage-dotnet-how-to-use-blobs.md).

De forma parecida, você pode baixar dados de segurança armazenados nas tabelas do Azure usando a Biblioteca de Cliente do Armazenamento do Azure. Para saber mais sobre como acessar os dados armazenados em tabelas, consulte [Como usar o armazenamento de tabela no .NET](storage-dotnet-how-to-use-tables.md).

## Relatórios do Active Directory do Azure
O Active Directory do Azure (AD do Azure) inclui um conjunto de relatórios de segurança, uso e de log de auditoria que fornecem informações sobre a integridade e a segurança de seu locatário do AD do Azure. Por exemplo, o AD do Azure tem a capacidade de analisar automaticamente a atividade do usuário, mostrar os acessos anormais e disponibilizá-los por meio de relatórios visíveis ao cliente.

Esses relatórios ficam disponíveis por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) em **Active Directory** > **Diretório**. Alguns desses relatórios são gratuitos, e outros são oferecidos como parte de uma edição Premium do AD do Azure. Para saber mais sobre os relatórios do AD do Azure, consulte [Exibir relatórios de acesso e de uso](http://msdn.microsoft.com/library/azure/dn283934.aspx).

## Logs de operação do Azure
Os logs para operações relacionadas aos recursos de sua assinatura do Azure também estão disponíveis por meio do recurso **Logs de Operação** no portal de gerenciamento.

Para exibir os **Logs de Operação**, no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/), clique em **Serviços de Gerenciamento** e clique em **Logs de Operação**.

## <a name="diagnostics"></a> Fontes de dados com suporte do Diagnóstico do Azure

| Fonte de dados | Descrição |
|----- | ----- |
| Logs IIS | Informações sobre sites do IIS |
| Logs de infraestrutura de diagnóstico do Azure | Informações sobre o Diagnóstico do Azure |
| Logs de solicitação com falha IIS | Informações sobre solicitações com falha para um site ou aplicativo do IIS |
| Log de eventos do Windows | Informações enviadas ao sistema de log de eventos do Windows |
| Contadores de desempenho | Contadores de desempenho personalizados e do sistema operacional |
| Despejos de falhas | Informações sobre o estado do processo no caso de uma falha do aplicativo |
| Logs de erros personalizados | Logs criados por seu aplicativo ou serviço |
| .NET EventSource | Eventos gerados por seu código usando a classe EventSource do .NET |
| ETW baseado em manifesto | Rastreamento de Eventos para eventos do Windows gerados por qualquer processo |

## Recursos adicionais
Os recursos a seguir fornecem informações gerais sobre o Microsoft Azure e serviços da Microsoft relacionados:

- [Central de confiabilidade do Microsoft Azure](http://azure.microsoft.com/support/trust-center/)

    Informações sobre como a segurança e a privacidade são incorporadas ao desenvolvimento do Azure e como o Azure atende a um amplo conjunto de padrões de conformidade internacionais e específicos do setor

- [Página inicial do Microsoft Azure](http://www.microsoft.com/windowsazure/)

    Informações gerais e links sobre o Microsoft Azure

- [Centro de documentação do Microsoft Azure](http://msdn.microsoft.com/windowsazure/default.aspx)

    Orientação para serviços e scripts de automação do Azure

- [Microsoft Security Response Center (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    O MSRC trabalha com parceiros e pesquisadores de segurança em todo o mundo para ajudar a evitar incidentes de segurança e a aprimorar a segurança dos produtos Microsoft

- [Email do Microsoft Security Response Center](mailto:secure@microsoft.com)

    Envie por email um relatório sobre as vulnerabilidades de segurança da Microsoft, incluindo o Microsoft Azure

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_1217_2015-->