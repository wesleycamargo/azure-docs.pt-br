<properties
   pageTitle="Analisar dados de servidores no Microsoft Azure"
   description="Pesquisar eventos e logs do IIS para os serviços de nuvem e máquinas virtuais habilitando o Diagnóstico do Azure."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders"/>
# Analisar dados de servidores no Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

O recurso Insights Operacionais usa dados de servidores na sua infraestrutura local ou na nuvem. Você pode coletar dados da máquina do armazenamento do Azure quando gerados pelo diagnóstico do Azure.

Usando os dados coletados do Armazenamento do Azure, você pode pesquisar rapidamente os logs de evento e do IIS para os serviços de nuvem e máquinas virtuais. Você também pode obter informações adicionais de suas máquinas virtuais instalando o Microsoft Monitoring Agent.

As soluções Avaliação de Atualização, Controle de Alterações e Avaliação de SQL funcionam com o Microsoft Monitoring Agent para fornecer insights mais profundos sobre suas máquinas virtuais. Se ainda não tiver feito isso, você pode [adicionar soluções](operational-insights-setup-workspace.md) após entrar no [Portal de Insights Operacionais](https://www.microsoft.com/oms/).

Para máquinas virtuais do Azure, há duas formas fáceis de habilitar a coleta de dados com base em agente:

- No Portal de Gerenciamento do Microsoft Azure

- Usando o PowerShell

Ao usar a coleta com base em agente para dados de log, você deve configurar quais logs coletar na página de configuração de Gerenciamento de Log do [Portal dos Insights Operacionais](https://www.microsoft.com/oms/).

 >[AZURE.NOTE]Se você tiver configurado o Insights Operacionais para indexar dados de log usando os diagnósticos do Azure e configurar o agente para coletar logs, os mesmos logs serão indexados duas vezes. Você será cobrado de acordo com as taxas de dados normais para ambas as fontes de dados. Se você tiver o agente instalado, deverá coletar dados de log usando o agente e não indexar os logs coletados pelos diagnósticos do Azure.

## Portal de Gerenciamento do Microsoft Azure

Você pode instalar o agente para Insights Operacionais no [Portal do Azure](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces).

### Para instalar o agente para Insights Operacionais
1. No Portal do Azure, vá para seu espaço de trabalho de Insights Operacionais e selecione a guia **Servidores**.
2. Na guia, você verá uma lista de máquinas virtuais. Selecione a máquina virtual na qual deseja instalar o agente e, em seguida, clique em **Habilitar Insights Op**.

O agente é automaticamente instalado e configurado para o seu espaço de trabalho de Insights Operacional.

![Imagem da página Servidores de Insights Operacionais](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]O [Agente de VM do Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) deve estar instalado para instalar automaticamente o agente para Insights Operacionais.



## PowerShell

Se você preferir que o script faça alterações em suas máquinas virtuais do Azure, habilite o Microsoft Monitoring Agent usando o PowerShell.

O Microsoft Monitoring Agent é uma [extensão de máquina virtual do Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) e você pode gerenciá-lo usando o PowerShell, conforme mostrado no exemplo abaixo.

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Ao configurar usando o PowerShell, você precisa fornecer a ID do espaço de trabalho e a Chave Primária. Você pode encontrar a ID do Espaço de Trabalho e a Chave Primária na página **Configurações** do portal de Insights Operacionais.

![fontes](./media/operational-insights-analyze-data-azure/sources01.png)

## Coletar dados usando diagnósticos do Azure

O Insights Operacionais pode analisar os dados gravados no armazenamento do Azure pelos diagnósticos do Azure. Há duas etapas principais para executar:

- Configurar a coleta de dados de diagnóstico para o armazenamento do Azure
- Configurar Insights Operacionais para analisar os dados na conta de armazenamento

Os tópicos a seguir descrevem como habilitar a coleta de dados de diagnóstico no armazenamento do Azure e como configurar Insights Operacionais para analisar os dados no armazenamento do Azure.

Os Diagnósticos do Azure são uma extensão do Azure que permite coletar dados de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser usados por Insights Operacionais.

>[AZURE.NOTE]Você será cobrado de acordo com taxas de dados normais para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento e quando o Insights Operacionais ler os dados de sua conta de armazenamento.

O Diagnóstico do Azure pode coletar os seguintes tipos de telemetria:

Fonte de dados|Descrição
 ---|---
Logs IIS|Informações sobre sites IIS.
Logs de infraestrutura de diagnóstico do Azure|Informações sobre o próprio Diagnostics.
Logs de solicitação com falha IIS |Informações sobre solicitações falhas para um site IIS ou aplicativo.
Log de eventos do Windows|Informações enviadas ao sistema de registro de evento do Windows.
Contadores de desempenho|Sistema Operacional e contadores de desempenho personalizados.
Despejos de falhas|Informações sobre o estado do processo no evento de uma falha do aplicativo.
Logs de erros personalizados|Logs criados por seu aplicativo ou serviço.
NET EventSource|Os eventos gerados pelo seu código usando .NET [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx)
Manifesto com base no ETW|Eventos ETW gerados por qualquer processo.
Syslog|Eventos enviados para os daemons Syslog ou Rsyslog


Atualmente, o Insights Operacionais é capaz de analisar:

- Logs do IIS de funções da Web e máquinas virtuais
- Logs de eventos do Windows de funções da Web, funções de trabalho e máquinas virtuais do Azure executando um sistema operacional Windows
- Syslog de máquinas virtuais do Azure executando um sistema operacional Linux

Os logs devem estar nos seguintes locais:

- WADWindowsEventLogsTable (armazenamento em tabela) – contém informações de logs de eventos do Windows.
- wad-iis-logfiles (armazenamento do blob) - contém informações de logs do IIS.
- LinuxsyslogVer2v0 (armazenamento de tabela) – contém eventos de syslog do Linux.

 >[AZURE.NOTE]Atualmente, não há suporte para logs do IIS nos sites do Azure.

Para máquinas virtuais, você também tem a opção de instalar o [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) na sua máquina virtual para habilitar insights adicionais. Além de ser capaz de analisar os logs do IIS e Logs de Eventos, você também poderá executar análises adicionais, inclusive controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

Você pode nos ajudar a priorizar logs adicionais para os Insights Operacionais para análise votando em nossa [página de comentários](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Habilitar diagnóstico do Azure em uma função web para coleta de eventos e log do IIS

Consulte [Como habilitar o diagnóstico em um Serviço de Nuvem](https://msdn.microsoft.com/library/azure/dn482131.aspx). Você usa as informações básicas de lá e personaliza as etapas aqui para usar com o Insights Operacionais do Microsoft Azure.

Com o diagnóstico do Azure habilitado:

- Logs do IIS são armazenados por padrão, e os dados de log são transferidos ao intervalo de transferência de scheduledTransferPeriod.

- Logs de eventos do Windows não são transferidos por padrão.

### Para habilitar o diagnóstico

Para habilitar Logs de Eventos do Windows ou para alterar o scheduledTransferPeriod, configure os Diagnósticos do Azure usando o arquivo de configuração XML (diagnostics.wadcfg), conforme mostrado na [Etapa 2: adicionar o arquivo diagnostics.wadcfg à sua solução do Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) e na [Etapa 3: configurar o diagnóstico para seu aplicativo](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) no tópico Como habilitar os diagnósticos em um Serviço de Nuvem. O arquivo de configuração de exemplo a seguir coleta Logs do IIS e todos os Eventos dos logs do aplicativo e do sistema:

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


Na [Etapa 4: configurar o armazenamento dos seus dados de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) do tópico Como habilitar o diagnóstico em um Serviço de Nuvem, certifique-se de que seu ConfigurationSettings especifique uma conta de armazenamento, como mostrado no exemplo a seguir:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Os valores **AccountName** e **AccountKey** são encontrados no Portal de Gerenciamento do Microsoft Azure, no painel da conta de armazenamento, em Gerenciar Chaves de Acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois de a configuração de diagnóstico atualizada ser aplicada ao serviço de nuvem e você estar gravando o diagnóstico no armazenamento do Azure, você estará pronto para configurar o Insights Operacionais.

## Habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS

Use o procedimento a seguir para habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o Portal de Gerenciamento do Microsoft Azure.

### Para habilitar os diagnósticos do Azure em uma máquina virtual com o Portal de Gerenciamento do Azure

1. Instale o Agente de VM quando criar uma máquina virtual. Se a máquina virtual já existir, verifique se o Agente de VM já está instalado.
	- Se você usar o Portal de Gerenciamento do Azure padrão para criar a máquina virtual, execute uma **Criação Personalizada** e selecione **Instalar o Agente de VM**.
	- Se você usar o novo Portal de Gerenciamento do Azure para criar uma máquina virtual, selecione **Configuração Opcional**, **Diagnóstico** e defina o **Status** como **Ativo**.

	Após a conclusão, a VM terá automaticamente a extensão de diagnóstico do Azure instalada e em execução, que será responsável por coletar os dados de diagnóstico.

2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e, em seguida, configurar o log de eventos, execute as seguintes etapas:
	1. Selecione a VM.
	2. Clique em **Monitoramento**.
	3. Clique em **Diagnóstico**.
	4. Defina o **Status** como **ATIVO**.
	5. Selecione cada métrica de diagnóstico que deseja usar. O Insights Operacionais podem analisar logs do sistema de eventos do Windows, logs de aplicativos de eventos do Windows e logs do IIS.
	7. Clique em **OK**.

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure. Consulte o esquema de configuração do Diagnóstico do Azure 1.2 para um exemplo de arquivo de configuração e documentação detalhada sobre seu esquema. Certifique-se de instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior em [Como instalar e configurar o Azure PowerShell](powershell-install-configure). Se você tiver uma versão do Diagnóstico do Microsoft Azure instalada anterior à versão 1.2, você não pode usar o novo portal para habilitar ou configurar o diagnóstico.

Você pode habilitar e atualizar o agente usando o seguinte script do PowerShell. Você também pode usar esse script com a configuração de log personalizada. Você precisará modificar o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.

### Para habilitar o diagnóstico em uma máquina virtual com o PowerShell do Azure

Revise o exemplo de script a seguir, copie-o, modifique-o conforme necessário, salve o exemplo como um arquivo de script do PowerShell e execute o script.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Habilite a análise de armazenamento do Azure por Insights Operacionais

Use o procedimento a seguir para habilitar a análise de armazenamento e configurar o Insights Operacionais para a leitura da conta de armazenamento do Azure com o Diagnóstico do Azure.

### Para habilitar a análise de Insights Operacionais

1. No portal padrão do Azure, navegue para seu espaço de trabalho de Insights Operacionais e selecione a guia **Armazenamento**. ![guia armazenamento do espaço de trabalho](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Clique em **Adicionar uma Conta de Armazenamento** para abrir a caixa **Adicionar Conta de Armazenamento**.
3. Selecione a conta de armazenamento que você deseja usar.
4. Na lista **Tipo de Dados**, selecione um tipo de dados: **Eventos**, **Logs de IIS** ou **Syslog (Linux)**.
5. Clique na imagem **OK**. <br> ![caixa conta de armazenamento](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Repita as etapas acima para cada combinação de tipo de dados e conta de armazenamento que deseja coletar.

Em aproximadamente 1 hora, você começará a ver os dados da conta de armazenamento disponíveis para análise no Insights Operacionais.

## Conteúdo relacionado

- [Conectar computadores diretamente a Insights Operacionais](operational-insights-direct-agent)
- [Postagem do blog: habilitar Insights Operacionais para máquinas virtuais do Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Próximas etapas

[Definir as configurações de Proxy e Firewall (opcional)](../operational-insights-proxy-filewall.md)

<!---HONumber=06-->