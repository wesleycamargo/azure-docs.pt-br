<properties 
   pageTitle="Coletar dados de computador" 
   description="Colete dados de computador de servidores na sua infraestrutura local ou na nuvem" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
# Coletar dados de computador

O recurso Insights Operacionais usa dados de servidores na sua infraestrutura local ou na nuvem. Você pode coletar dados de computador das seguintes fontes:

- Agentes do Operations Manager
- Computadores conectados diretamente ao recurso Insights Operacionais
- Dados de diagnóstico de máquina virtual nos serviços de armazenamento do Azure

Após a coleta dos dados, eles são enviados para o serviço Insights Operacionais.

## Conectar-se a Insights Operacionais do System Center Operations Manager 

Você pode se conectar ao Insights operacional em um ambiente existente do System Center Operations Manager. Isso permitirá usar os agentes do Operations Manager existentes como os agentes doe Insights Operacionais. Para obter informações adicionais sobre como usar o Operations Manager com Insights Operacionais, consulte [Considerações sobre o Operations Manager com Insights Operacionais](operational-insights-operations-manager.md).

Se você usar o Operations Manager para monitorar qualquer uma das seguintes cargas de trabalho, precisará definir as contas de executar como do Operations Manager para elas. Consulte [Contas de executar como do Operations Manager para Insights Operacionais](operational-insights-run-as.md) para obter mais informações sobre como configurar as contas.

- Avaliação do SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Suporte para Insights Operacionais está disponível no Operations Manager 2012 SP1 UR6 e UR2 e Operations Manager 2012 R2. Foi adicionado suporte de proxy no System Center Operations Manager 2012 SP1 UR7 e System Center Operations Manager 2012 R2 UR3.

#### Para conectar o Operations Manager a Insights Operacionais e adicionar agentes

1. No console do Operations Manager, clique em **Administração**.

2. Expanda o **Insights Operacionais** nó e clique em **Conexão do Insights Operacionais**.

3. Clique no link **Registrar-se em Insights Operacionais** e siga as instruções na tela. 

4. Depois de concluir o assistente de registro, clique em **Adicionar um computador/grupo**.

5. Na caixa de diálogo **Pesquisa de Computador**, você pode pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione os computadores ou grupos para carregar no Insights Operacionais, clique em **Adicionar** e em **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Conectar computadores diretamente a Insights Operacionais 

Você pode conectar computadores diretamente a Insights Operacionais instalando o agente Insights Operacionais em cada computador que você desejar integrar. 


### Baixe e instale o agente

#### Para baixar o arquivo de instalação do agente
1. No **Portal de Insights Operacionais**, em **Visão Geral**, clique em **Servidores e Uso**.
1. Em **Servidores conectados diretamente**, clique em **configurar**.
1. Ao lado de **Adicionar Agentes**, clique no link Agente para baixar o arquivo de instalação.
1. Na caixa **Chave Primária do Espaço de Trabalho**, selecione a chave e copie-a (CTRL+C).


#### Para instalar o agente usando o programa de instalação
1. Execute Instalação para instalar o agente em um computador que você deseje gerenciar.
1. Selecione **Conectar o agente a Insights Operacionais do Microsoft Azure** e clique em **Avançar**.
1. Quando solicitado, insira as informações copiadas na etapa 4.
1. Ao concluir, o **Agente de Gerenciamento da Microsoft** aparece no **Painel de Controle**.

#### Para instalar o agente usando a linha de comando
Modifique e, em seguida, use o exemplo a seguir para instalar o agente usando a linha de comando.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Configure o Microsoft Monitoring Agent (opcional)
Use as informações a seguir para habilitar um agente para se comunicar diretamente com o serviço de Insights Operacionais do Microsoft Azure. Depois que você tiver configurado o agente, ele será registrado com o serviço do agente e obterá as informações de configuração necessárias e os pacotes de gerenciamento contendo informações do pacote de inteligência.

Depois que dados são coletados de computadores monitorados por agente, o número de computadores monitorados aparecerá no portal de Insights Operacionais na página Uso sob **Agentes Conectados Diretamente**. Para qualquer computador que envie dados, você pode exibir suas informações de avaliação e dados no portal de Insights Operacionais.

Você também pode desabilitar o agente, se necessário, ou habilitá-lo usando a linha de comando ou script.

#### Para configurar o agente
- Depois de instalar o **Microsoft Monitoring Agent**, abra o **painel de controle**.
- Abra o Microsoft Monitoring Agent e, em seguida, clique na guia Insights Operacionais do Azure.
- Selecione **Conectar a Insights Operacionais do Azure**.
- Na caixa **ID do espaço de trabalho**, digite a ID do espaço de trabalho fornecida no portal de Insights Operacionais.
- Na caixa Chave de Conta, cole a **Chave Primária do Espaço de Trabalho** copiada quando você instalou o agente e clique em **OK**.

#### Para desabilitar um agente
- Depois de instalar o agente, abra o **Painel de Controle**.
- Abra o Microsoft Monitoring Agent e, em seguida, clique na guia **Insights Operacionais do Azure**.
- Desmarque Conectar-se a **Insights Operacionais do Azure**.

#### Para habilitar o agente usando a linha de comando ou script
Você pode usar o Windows PowerShell ou um script VB com o exemplo a seguir.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Definir as configurações de proxy e Firewall (opcional)
Se você tiver servidores proxy ou firewalls em seu ambiente que restrinjam o acesso à Internet, pode ser necessário realizar os procedimentos a seguir para habilitar o Operations Manager e/ou os agentes para se comunicarem com o serviço de Insights Operacionais 



- [Definir as configurações de proxy e firewall (opcional)](operational-insights-proxy-filewall.md) 

## Analisar dados de servidores no Microsoft Azure

Com Insights Operacionais, você pode rapidamente pesquisar eventos e logs do IIS para serviços de nuvem e máquinas virtuais, permitindo o [Diagnóstico nos Serviços de Nuvem do Azure](operational-insights-log-collection.md). Você também pode obter informações adicionais de suas máquinas virtuais instalando o Microsoft Monitoring Agent.

Os pacotes de inteligência Avaliação de Atualização, Controle de Alterações e Avaliação de SQL funcionam com o Microsoft Monitoring Agent para fornecer insights mais profundos sobre suas máquinas virtuais. Se você ainda tiver feito isso, pode habilitar esses pacotes de inteligência quando tiver entrado no [Portal de Insights Operacionais](https://preview.opinsights.azure.com/).

Para máquinas virtuais do Azure, há duas formas fáceis de habilitar a coleta de dados com base em agente:

- No Portal de Gerenciamento do Microsoft Azure

- Usando o PowerShell

Ao usar a coleta com base em agente para dados de log, configure quais logs coletar na página de configuração de Gerenciamento de log do [Portal de Insights Operacionais](https://preview.opinsights.azure.com/).

Se você tiver configurado o Insights Operacionais para indexar dados de log usando o diagnóstico nos Serviços de Nuvem do Azure e você configurar o agente para coletar logs, os mesmos logs de eventos terão duas cópias dos dados indexados. Se você tiver o agente instalado, deve coletar dados de log usando o agente e não indexar os logs coletados pelo diagnóstico de Serviços de Nuvem do Azure.

### Portal de Gerenciamento do Microsoft Azure

No [Portal de Insights Operacionais](https://preview.opinsights.azure.com/), Vá para seu espaço de trabalho de Insights Operacionais e selecione a guia Servidores. Na guia, você verá uma lista de máquinas virtuais. Selecione a máquina virtual em que deseja instalar o agente e, em seguida, clique em **Habilitar Insights Op**.

O agente é instalado e configurado para o seu espaço de trabalho de Insights Operacional.

![Imagem da página Servidores de Insights Operacionais](./media/operational-insights-collect-data/servers.png)

### PowerShell

Se você preferir que o script faça alterações em suas máquinas virtuais do Azure, habilite o Microsoft Monitoring Agent usando o PowerShell.

O Microsoft Monitoring Agent é uma [extensão da máquina virtual do Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) e pode gerenciá-la usando o PowerShell, conforme mostrado no exemplo a seguir.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

Ao configurar usando o PowerShell, você precisa fornecer a ID do espaço de trabalho e a chave do espaço de trabalho. Você pode encontrar a ID do espaço de trabalho e a chave do espaço de trabalho na página Servidores e Uso do portal de Insights Operacionais.

![Imagem da configuração de agente direto do Insights Operacionais](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Coletar dados usando diagnóstico nos Serviços de Nuvem do Azure

O Insights Operacionais pode analisar dados gravados pelo diagnóstico dos Serviços de Nuvem do Azure para os serviços de Armazenamento do Azure. Há duas etapas principais para executar:

- Configurar a coleta de dados de diagnóstico para o armazenamento do Azure
- Configurar Insights Operacionais para analisar os dados na conta de armazenamento

Os tópicos a seguir descrevem como habilitar a coleta de dados de diagnóstico no armazenamento do Azure e como configurar Insights Operacionais para analisar os dados no armazenamento do Azure.

Os Diagnósticos do Azure são uma extensão do Azure que permite coletar dados de telemetria de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados de telemetria são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser usados por Insights Operacionais.

>[AZURE.NOTE] Você deverá pagar taxas de dados normais para armazenamento e transações quando envia o diagnóstico para uma conta de armazenamento.

O Diagnóstico do Azure pode coletar os seguintes tipos de telemetria:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Fonte de dados</b></td>
		<td><b>

Descrição </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Logs do IIS</td>
		<td>Informações sobre sites IIS.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Logs de infraestrutura do Diagnóstico do Azure</td>
		<td>Informações sobre o próprio Diagnóstico.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Logs de solicitação de falha do IIS </td>
		<td>Informações sobre falhas em solicitações para um site ou aplicativo IIS.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Logs de Eventos do Windows</td>
		<td>Informações enviadas ao sistema de registro de eventos do Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Contadores de desempenho</td>
		<td>Contadores de desempenho personalizados e do Sistema operacional.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Despejos de memória</td>
		<td>Informações sobre o estado do processo em caso de falha do aplicativo.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Logs de erros personalizados</td>
		<td>Logs criados por seu aplicativo ou serviço.</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>Os eventos de log gerados pelo seu código usando a classe .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource/a></td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW baseado em manifesto</td>
		<td>Eventos ETW gerados por qualquer processo.</td>
    </tr>
    </tbody>
    </table>


Atualmente, o Insights Operacionais é capaz de analisar logs do IIS de funções web e logs de eventos do Windows de funções web e de trabalho e máquinas virtuais do Azure. Os logs devem estar nos seguintes locais:

- WADWindowsEventLogsTable (armazenamento em tabela) - contém informações de logs de eventos do Windows.

- wad-iis-logfiles (armazenamento do blob) - contém informações de logs do IIS.

Para máquinas virtuais, você também tem a opção de instalar o [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) em sua máquina virtual. Isso permitirá analisar logs do IIS, logs de eventos e também executar análises adicionais, inclusive o controle de alterações de configuração e avaliação da atualização. Habilitar informações adicionais instalando o [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] Atualmente, não há suporte para logs do IIS nos sites do Azure.

Você pode nos ajudar a priorizar logs adicionais de do Operacionais para analisar votando na [página de comentários](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Habilitar diagnóstico do Azure em uma função web para coleta de eventos e log do IIS

Consulte [Como habilitar o diagnóstico em um serviço de nuvem](https://msdn.microsoft.com/library/azure/dn482131.aspx). Você usa as informações básicas de lá e personaliza as etapas aqui para usar com o Insights Operacionais do Microsoft Azure.

Com o diagnóstico do Azure habilitado:

- Logs do IIS são armazenados por padrão, e os dados de log são transferidos ao intervalo de transferência de scheduledTransferPeriod.

- Logs de eventos do Windows não são transferidos por padrão.

#### Para habilitar o diagnóstico

Para habilitar Logs de eventos do Windows ou alterar o scheduledTransferPeriod, configure o diagnóstico do Azure usando o arquivo de configuração XML (wadcfg), conforme mostra a [Etapa 2: Adicionar o arquivo diagnostics.wadcfg à sua solução do Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) e a [Etapa 3: Configurar o diagnóstico para seu aplicativo](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) no tópico Como habilitar o diagnóstico em um serviço de nuvem. O exemplo de arquivo de configuração a seguir coleta logs do IIS e todos os eventos dos logs do aplicativo e do sistema:

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">
     
      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>
     
      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>
     
    </DiagnosticMonitorConfiguration>


Na [Etapa 4: Configurar o armazenamento dos seus dados de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) do tópico Como habilitar o diagnóstico em um serviço de nuvem, certifique-se de que ConfigurationSettings especifique uma conta de armazenamento, como no exemplo a seguir:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Os valores **AccountName** e **AccountKey** são encontrados no Portal de Gerenciamento do Microsoft Azure, no painel da conta de armazenamento, em Gerenciar Chaves de Acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois de a configuração de diagnóstico atualizada ser aplicada ao serviço de nuvem e você estar gravando o diagnóstico no armazenamento do Azure, você estará pronto para configurar o Insights Operacionais.

### Habilitar o diagnóstico dos Serviços de Nuvem do Azure em uma máquina virtual para coleta de log de eventos e log do IIS

Use o procedimento a seguir para habilitar o diagnóstico de Serviços de Nuvem do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o Portal de Gerenciamento do Microsoft Azure.

#### Para habilitar o diagnóstico de Serviços de Nuvem do Azure em uma máquina virtual com o Portal de Gerenciamento do Azure

1. Instale o Agente de VM quando criar uma máquina virtual. Se a máquina virtual já existir, verifique se o Agente de VM já está instalado.
	- Se você usar o Portal de Gerenciamento do Azure padrão para criar a máquina virtual, execute uma **Criação Personalizada** e selecione **Instalar o Agente de VM**.
	- Se você usar o novo Portal de Gerenciamento do Azure para criar uma máquina virtual, selecione **Configuração Opcional** e, em seguida, **Diagnóstico** e defina **Status** para **Ligado**.
	
	Após a conclusão, a VM terá automaticamente a extensão de diagnóstico do Azure instalada e em execução, que será responsável por coletar os dados de diagnóstico.

2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e, em seguida, configurar o log de eventos, execute as seguintes etapas:
	1. Selecione a VM.
	2. Clique em **Monitoramento**.
	3. Clique em **Diagnóstico**.
	4. Defina o **Status** para **LIGADO**.
	5. Selecione cada métrica de diagnóstico que deseja usar. O Insights Operacionais podem analisar logs do sistema de eventos do Windows, logs de aplicativos de eventos do Windows e logs do IIS.
	7. Clique em **OK**.

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure. Consulte o esquema de configuração do Diagnóstico do Azure 1.2 para um exemplo de arquivo de configuração e documentação detalhada sobre seu esquema. Certifique-se de instalar e configurar o PowerShell do Azure versão 0.8.7 ou posterior em [Como instalar e configurar o PowerShell do Azure](install-configure-powershell/). Se você tiver uma versão do Diagnóstico do Microsoft Azure instalada anterior à versão 1.2, você não pode usar o novo portal para habilitar ou configurar o diagnóstico.

Você pode habilitar e atualizar o agente usando o seguinte script do PowerShell. Você também pode usar esse script com a configuração de log personalizada. Você precisará modificar o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.

#### Para habilitar o diagnóstico em uma máquina virtual com o PowerShell do Azure

Revise o exemplo de script a seguir, copie-o, modifique-o conforme necessário, salve o exemplo como um arquivo de script do PowerShell e execute o script.

    # Conecte-se ao Azure
    Add-AzureAccount 
     
    # configurações a alterar:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    #Crie a configuração pública do Diagnóstico do Azure e converta para o formato de configuração 
    
    # Colete apenas os eventos de erro do sistema:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Crie a configuração particular do Diagnóstico do Azure
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #Habilite a extensão de diagnóstico para máquina virtual
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # obtém a versão da extensão
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### Habilite a análise de armazenamento do Azure por Insights Operacionais

No portal do Azure padrão, navegue para seu espaço de trabalho do Insights Operacionais e selecione a guia **Armazenamento**. Use as informações a seguir para configurar o Insights Operacionais a ler da conta de Armazenamento do Azure com o Diagnóstico do Azure.

#### Para habilitar a análise de Insights Operacionais

1. Clique em **Adicionar** para abrir a caixa **Adicionar Conta de Armazenamento**.

2. Selecione a **Conta de Armazenamento**.

3. Selecione um tipo de dados, **eventos** ou **Logs do IIS**.

4. Clique em **OK**.

5. Repita as etapas acima para cada combinação de tipo de dados e conta de armazenamento que deseja coletar.

Em aproximadamente 1 hora, você começará a ver os dados da conta de armazenamento disponíveis para análise no Insights Operacionais.




## Conteúdo relacionado

- [Postagem no blog: Conectar servidores diretamente aos Insights Operacionais](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Postagem no blog: Habilitar Insights Operacionais para máquinas virtuais do Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Próximas etapas

[Definir as configurações de Proxy e Firewall (opcional)](operational-insights-proxy-filewall.md)



<!--HONumber=52-->