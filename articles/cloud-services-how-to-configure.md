<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu" />

# <span id="configurecloudservice"></span></a>Como configurar serviços de nuvem

[WACOM.INCLUDE [isenção de responsabilidade](../includes/disclaimer.md)]

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no Portal de Gerenciamento do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

Você também pode habilitar uma conexão de área de trabalho remota para uma ou todas as funções em execução no Serviço de Nuvem. A área de trabalho remota permite que você acesse área de trabalho do seu aplicativo durante a execução e solucione e diagnostique problemas. Você pode habilitar uma conexão de área de trabalho remota para sua função mesmo sem ter configurado um arquivo de definição de serviço (.csdef) para a área de trabalho remota durante o desenvolvimento do aplicativo. Não é necessário reimplantar seu aplicativo para habilitar uma conexão de área de trabalho remota.

O Azure poderá garantir apenas 99,95 por cento de disponibilidade de serviço durante as atualizações da configuração se você tiver pelo menos duas instâncias de função (máquinas virtuais) para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço][Contratos de Nível de Serviço].

## Sumário

-   [Como: atualizar o arquivo de configuração do serviço de nuvem][Como: atualizar o arquivo de configuração do serviço de nuvem]
-   [Como: Configurar o acesso remoto para instâncias de função][Como: Configurar o acesso remoto para instâncias de função]

## <span id="update"></span></a>Como: Atualizar o arquivo de configuração do serviço de nuvem

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

2.  Clique em **Configurar**.

    Na página **Configurar**, é possível configurar o monitoramento, atualizar as configurações da função e escolher o sistema operacional convidado e a família para instâncias de função (máquinas virtuais).

    ![Página de configuração][Página de configuração]

3.  Em configurações do monitoramento, defina o nível de monitoramento como Detalhado ou Mínimo e configure as cadeias de conexão de diagnóstico necessárias para o monitoramento detalhado. Para obter instruções, consulte [Como monitorar serviços de nuvem][Como monitorar serviços de nuvem].

4.  Para as funções de serviço (agrupadas por função), você pode atualizar as seguintes configurações:

    > -   **Configurações** Modifique os valores das diversas especificadas nos elementos *ConfigurationSettings* do arquivo de configuração do serviço (.cscfg).

    > -   **Certificados** Altere a impressão digital do certificado usado na criptografia SSL para uma função. Para alterar um certificado, você deve primeiro carregar o novo certificado (na página **Certificados**). Em seguida, atualize a impressão digital na cadeia de caracteres do certificado exibida nas configurações da função.

5.  Em **configurações do sistema operacional**, você pode alterar a família ou a versão do sistema operacional para instâncias de função (máquinas virtuais) ou escolher **Automático** para continuar as atualizações automáticas da versão atual do sistema operacional. As configurações do sistema operacional aplicam-se às funções Web e às funções de trabalho mas não afetam as funções VM que foram adicionadas aos serviços hospedados no Portal de Gerenciamento do Azure anterior.

    Ao implantar um novo Serviço de Nuvem, você pode escolher o sistema operacional Windows Server 2008 R2, Windows Server 2008 com Service Pack 2 (SP2) ou Windows Server 2012. Durante a implantação, a versão mais recente do sistema operacional é instalada em todas as instâncias de função e os sistemas operacionais são atualizados automaticamente por padrão.

    Se precisar que o Serviço de Nuvem seja executado em outra versão do sistema operacional devido a requisitos de compatibilidade com seu código, você poderá escolher uma família e versão do sistema operacional. Ao escolher uma versão específica do sistema operacional, as atualizações automáticas do sistema operacional são suspensas para o serviço de nuvem. Você precisará garantir que os sistemas operacionais recebam atualizações.

    Se você resolver todos os problemas da compatibilidade de seus aplicativos com a versão mais recente do sistema operacional, poderá continuar as atualizações automáticas do sistema operacional definindo a versão do sistema operacional como **Automático**.

    ![Definições do sistema operacional][Definições do sistema operacional]

6.  Para salvar as configurações e enviá-las por push para as instâncias de função, clique em **Salvar**. (Clique em **Descartar** para cancelar as alterações.) **Salvar** e **Descartar** são adicionados à barra de comandos após a alteração de uma configuração.

### Para atualizar manualmente um arquivo de configuração de serviço de nuvem

1.  Baixe um arquivo de configuração do Serviço de Nuvem (.cscfg) com a configuração atual. Na página **Configurar** do Serviço de Nuvem, clique em **Baixar**. Em seguida, clique em **Salvar** ou em **Salvar como** para salvar o arquivo.

2.  Após atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações da configuração:

    a. Na página **Configurar**, clique em **Carregar**.

    **Carregar um Novo Arquivo de Configuração** é exibido.

    ![Carregamento da configuração][Carregamento da configuração]

    b. Em **Arquivo de configuração**, use **Procurar** para selecionar o arquivo .cscfg atualizado.

    c. Se seu Serviço de Nuvem contiver alguma função com apenas uma instância, marque a caixa de seleção **Aplicar configuração mesmo se uma ou mais funções contiverem uma única instância** para habilitar as atualizações de configuração para que funções continuem.

    A menos que você defina no mínimo duas instâncias de cada função, o Azure não poderá garantir ao menos 99,95 por cento de disponibilidade do seu Serviço de Nuvem durante as atualizações da configuração do serviço. Para obter mais informações, consulte [Contratos de Nível de Serviço][1].

    d. Clique em OK (marca de seleção).

## <span id="remoteaccess"></span></a>Como: Configurar o acesso remoto para instâncias de função

A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar a conexão da área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo durante a execução. Você pode habilitar uma conexão de área de trabalho remota em sua função durante a criação do aplicativo ou após a implantação do aplicativo no Azure (com a função em execução). Habilitar uma conexão de área de trabalho remota em uma função em execução através do Portal de Gerenciamento não exige a reimplantação do seu aplicativo. Para autenticar a conexão de área de trabalho remota você pode usar um certificado carregado anteriormente ou criar um novo certificado.

Na página **Configurar** do seu Serviço de Nuvem, você pode habilitar a área de trabalho remota ou alterar a conta ou a senha do administrador local usada para conexão às máquinas virtuais, o certificado usado na autenticação ou a data de validade.

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Se seu Servi&ccedil;o de Nuvem consistir em duas ou mais m&aacute;quinas virtuais conectadas baseadas no Windows Server, n&atilde;o ser&aacute; necess&aacute;rio configurar o acesso remoto, pois essas m&aacute;quinas virtuais s&atilde;o configuradas automaticamente para a &aacute;rea de trabalho remota.</p> 
</div>

### Para configurar o acesso remoto no novo arquivo de definição de serviço

Adicione os elementos de **Importação** ao arquivo de definição de serviço (.csdef) para importar os módulos RemoteAccess e RemoteForwarder no modelo de serviço. Quando esses módulos estão presentes, o Azure adiciona as configurações da área de trabalho remota ao arquivo de configuração de serviço. Para concluir a configuração da área de trabalho remota, você precisará importar um certificado no Azure e especificar o certificado no arquivo de configuração de serviço. Para obter mais informações, consulte [Configuração de uma conexão de área de trabalho remota para uma função no Azure][Configuração de uma conexão de área de trabalho remota para uma função no Azure].

### Para habilitar ou modificar o acesso remoto para instâncias de função no Portal de Gerenciamento

1.  Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento do Azure] e clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

2.  Abra a página **Configurar** do Serviço de Nuvem e clique em **Remoto**.

    **Configurar Área de Trabalho Remota** exibe as configurações (se houver) adicionadas ao arquivo de configuração de serviço quando o Serviço de Nuvem foi implantado, conforme mostrado abaixo.

    ![Serviços de nuvem remotos][Serviços de nuvem remotos]

> [WACOM.NOTE]
> **Aviso:** Todas as instâncias de função serão reiniciadas quando você ativa área de trabalho remota pela primeira vez e clica em OK (marca de seleção). Para evitar a reinicialização, o certificado usado para criptografar a senha deve estar instalado na função. Se não houver nenhum certificado instalado, você verá esta opção: ![CloudServices\_CreateNewCertDropDown][CloudServices\_CreateNewCertDropDown]

    To prevent a restart, install a certificate and then return to this dialog (see [Using Remote Desktop with Azure Roles][Using Remote Desktop with Azure Roles] for more information). If you choose an existing certificate, then a configuration update will be sent to all the instances in the role.

1.  Em **Funções**, selecione a função do serviço que você deseja atualizar ou selecione **Tudo** para todas as funções.

2.  Faça algumas das seguintes alterações:

-   Para habilitar a área de trabalho remota, marque a caixa de seleção de **Habilitar Área de Trabalho Remota**. Para desabilitar a área de trabalho remota, desmarque a caixa de seleção.

-   Crie uma conta para usar nas conexões de área de trabalho remota para as instâncias de função.

-   Atualize a senha da conta existente.

-   Selecione um certificado carregado para usar a autenticação (carregue o certificado usando **Carregar** na página **Certificados**) ou crie um novo certificado.

-   Altere a data de validade para a configuração da área de trabalho remota.

1.  Ao concluir as atualizações da configuração, clique em OK (marca de seleção).

2.  Para conectar-se a uma instância de função:

    a. Clique em **Instâncias** para abrir a página **Instâncias**.

    b. Selecione uma instância de função com a área de trabalho remota configurada.

    c. Clique em **Conectar** e siga as instruções para abrir a área de trabalho da máquina virtual.

    d. Clique em **Abrir** e em **Conectar** para iniciar a conexão de área de trabalho remota.

### Para desabilitar ou modificar o acesso remoto para instâncias de função no Portal de Gerenciamento

1.  Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento do Azure] e clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

2.  Abra a página **Configurar** do Serviço de Nuvem e clique em **Remoto**.

3.  Em **Funções**, selecione a função do serviço que você deseja atualizar ou selecione **Tudo** para todas as funções.

4.  Desmarque ou limpe a caixa de seleção **Habilitar Conexão Remota**.

5.  Clique em OK (marca de seleção).

  [isenção de responsabilidade]: ../includes/disclaimer.md
  [Contratos de Nível de Serviço]: https://www.windowsazure.com/pt-br/support/legal/sla/
  [Como: atualizar o arquivo de configuração do serviço de nuvem]: #update
  [Como: Configurar o acesso remoto para instâncias de função]: #remoteaccess
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Página de configuração]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png
  [Como monitorar serviços de nuvem]: ../how-to-monitor-a-cloud-service/
  [Definições do sistema operacional]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png
  [Carregamento da configuração]: ./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png
  [1]: http://www.windowsazure.com/pt-br/support/legal/sla/
  [Configuração de uma conexão de área de trabalho remota para uma função no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh124107.aspx
  [Serviços de nuvem remotos]: ./media/cloud-services-how-to-configure/CloudServices_Remote.png
