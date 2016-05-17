<properties
	pageTitle="Conectar o armazenamento do Azure ao Log Analytics | Microsoft Azure"
	description="O Log Analytics usa dados de servidores em sua infraestrutura local ou na nuvem. Você pode coletar dados da máquina do armazenamento do Azure quando gerados pelo diagnóstico do Azure."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Conectar o armazenamento do Azure ao Log Analytics

O Log Analytics usa dados de servidores em sua infraestrutura local ou na nuvem. Você pode coletar dados da máquina do armazenamento do Azure quando gerados pelo diagnóstico do Azure.

Usando os dados coletados do Armazenamento do Azure, você pode pesquisar rapidamente os logs de evento e do IIS para os serviços de nuvem e máquinas virtuais. Você também pode obter informações adicionais de suas máquinas virtuais instalando o Microsoft Monitoring Agent.

As soluções Avaliação de Atualização, Controle de Alterações e Avaliação de SQL funcionam com o Microsoft Monitoring Agent para fornecer insights mais profundos sobre suas máquinas virtuais. Se você ainda não fez isso, é possível [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md) ao entrar no [portal do OMS](https://www.microsoft.com/oms/).

Para máquinas virtuais do Azure, há duas formas fáceis de habilitar a coleta de dados com base em agente:

- No Portal de Gerenciamento do Microsoft Azure
- Usando o PowerShell

Ao usar a coleta baseada em agente para dados de log, é necessário configurar quais logs serão coletados na página de configuração do Gerenciamento de Log do [portal do OMS](https://www.microsoft.com/oms/).

>[AZURE.NOTE] Se você tiver configurado o OMS para indexar dados de log usando o diagnóstico do Azure e se você configurar o agente para coletar logs, os mesmos logs serão indexados duas vezes. Você será cobrado de acordo com as taxas de dados normais para ambas as fontes de dados. Se você tiver o agente instalado, deverá coletar dados de log usando o agente e não indexar os logs coletados pelos diagnósticos do Azure.

## Portal do Microsoft Azure

É possível instalar o agente para OMS e conectar a máquina virtual do Azure na qual ele é executado usando o [portal do Azure](https://portal.azure.com).

### Para instalar o agente para OMS e conectar a máquina virtual a um espaço de trabalho

1.	Entre no [Portal do Azure](http://portal.azure.com).
2.	Procure pelo **Log Analytics (OMS)** e selecione-o.
3.	Em sua lista de espaços de trabalho do OMS, selecione aquele no qual você deseja que a VM do Azure se conecte.![espaços de trabalho do OMS](./media/log-analytics-azure-storage/oms-connect-azure-01.png)
4.	Em **Gerenciamento do Log Analytics**, clique em **Máquinas virtuais**.![máquinas virtuais](./media/log-analytics-azure-storage/oms-connect-azure-02.png)
5.	Na lista de **Máquinas virtuais**, selecione a máquina virtual em que deseja instalar o agente. O **status de conexão do OMS** para a VM indicará que ela **Não está conectada**.![VM não conectada](./media/log-analytics-azure-storage/oms-connect-azure-03.png)
6.	Nos detalhes de sua máquina virtual, clique em **Conectar**. O agente é instalado e configurado automaticamente para seu espaço de trabalho do OMS, mas pode levar alguns minutos até que o processo seja concluído.![conectar a VM](./media/log-analytics-azure-storage/oms-connect-azure-04.png)
7.	Quando o agente estiver instalado e conectado, você verá o status de **conexão do OMS** atualizado para mostrar **Neste espaço de trabalho**.![connected](./media/log-analytics-azure-storage/oms-connect-azure-05.png)

>[AZURE.NOTE] O [Agente de VM do Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) deve estar instalado para que o agente do OMS seja instalado automaticamente. Se você tiver uma máquina virtual do Azure Resource Manager, ela não será exibida na lista e você deverá usar o PowerShell ou criar um modelo ARM para instalar o agente.

Se você tiver VMs que não aparecem no portal de Gerenciamento do Azure, alguns possíveis motivos desse problema incluirão:

- A VM já é gerenciada por um espaço de trabalho diferente do OMS
- A VM é uma VM do ARM, para a qual não há suporte no momento

## PowerShell

Se você preferir que o script faça alterações em suas máquinas virtuais do Azure, habilite o Microsoft Monitoring Agent usando o PowerShell.

O Microsoft Monitoring Agent é uma [extensão de máquina virtual do Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) e você pode gerenciá-lo usando o PowerShell, como mostrado no exemplo abaixo.

Para máquinas virtuais clássicas do Azure, use o seguinte exemplo do PowerShell:

```
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```


Para máquinas virtuais do Azure Resource Manager, use o seguinte exemplo do PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"


$workspaceId="**"
$workspaceKey="**"

$resourcegroup = "**"
$resourcename = "**"

$vm = Get-AzureRMVM -ResourceGroupName $resourcegroup -Name $resourcename
$location = $vm.Location

Set-AzureRMVMExtension -ResourceGroupName $resourcegroup -VMName $resourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"


```
Ao configurar usando o PowerShell, você precisa fornecer a ID do espaço de trabalho e a Chave Primária. É possível encontrar a ID do Espaço de Trabalho e a Chave Primária na página **Configurações** do portal do OMS.

![ID do espaço de trabalho e chave primária](./media/log-analytics-azure-storage/oms-analyze-azure-sources.png)

## Coletar dados usando diagnósticos do Azure

O OMS pode analisar os dados gravados no armazenamento do Azure pelo diagnóstico do Azure. Há duas etapas principais para executar:

- Configurar a coleta de dados de diagnóstico para o armazenamento do Azure
- Configurar o OMS para analisar os dados na conta de armazenamento

Os tópicos abaixo descrevem como habilitar a coleta de dados de diagnóstico no armazenamento do Azure e como configurar o OMS para analisar os dados no armazenamento do Azure.

O Diagnóstico do Azure é uma extensão do Azure que permite coletar dados de diagnóstico de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser usados pelo OMS.

>[AZURE.NOTE] Se você tiver uma assinatura paga, você pagará de acordo com as taxas de dados normais de armazenamento e transações quando enviar o diagnóstico para uma conta de armazenamento e quando o OMS ler os dados de sua conta de armazenamento.

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
NET EventSource|Os eventos gerados pelo seu código usando a [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource%28v=vs.110%29.aspx) do .NET
Manifesto com base no ETW|Eventos ETW gerados por qualquer processo.
Syslog|Eventos enviados para os daemons Syslog ou Rsyslog


Atualmente, o OMS pode analisar:

- Logs do IIS de funções da Web e máquinas virtuais
- Logs de eventos do Windows de funções da Web, funções de trabalho e máquinas virtuais do Azure executando um sistema operacional Windows
- Syslog de máquinas virtuais do Azure executando um sistema operacional Linux

Os logs devem estar nos seguintes locais:

- WADWindowsEventLogsTable (armazenamento em tabela) – contém informações de logs de eventos do Windows.
- wad-iis-logfiles (armazenamento do blob) - contém informações de logs do IIS.
- LinuxsyslogVer2v0 (armazenamento de tabela) – contém eventos de syslog do Linux.

    >[AZURE.NOTE] Atualmente, não há suporte para logs do IIS nos sites do Azure.

Para máquinas virtuais, você também tem a opção de instalar o [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) na sua máquina virtual para habilitar insights adicionais. Além de ser capaz de analisar os logs do IIS e Logs de Eventos, você também poderá executar análises adicionais, inclusive controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

Você pode nos ajudar a priorizar logs adicionais para os OMS para análise votando em nossa [página de comentários](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Habilitar diagnóstico do Azure em uma função web para coleta de eventos e log do IIS

Consulte [Como habilitar o diagnóstico em um Serviço de Nuvem](https://msdn.microsoft.com/library/azure/dn482131.aspx). Você usará as informações básicas contidas na página e personalizará as etapas descritas aqui para uso com o OMS.

Com o diagnóstico do Azure habilitado:

- Logs do IIS são armazenados por padrão, e os dados de log são transferidos ao intervalo de transferência de scheduledTransferPeriod.

- Logs de eventos do Windows não são transferidos por padrão.

### Para habilitar o diagnóstico

Para habilitar os Logs de Eventos do Windows ou para alterar o scheduledTransferPeriod, configure o Diagnóstico do Azure usando o arquivo de configuração XML (diagnostics.wadcfg), como mostrado na [Etapa 2: Adicionar o arquivo diagnostics.wadcfg à sua solução do Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) e [Etapa 3: Configurar o diagnóstico para seu aplicativo](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) no tópico Como habilitar o diagnóstico em um Serviço de Nuvem. O arquivo de configuração de exemplo a seguir coleta os Logs do IIS e todos os Eventos dos logs do Aplicativo e do Sistema:

```
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
```

Na [Etapa 4: configurar o armazenamento dos dados de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) do tópico Como habilitar o diagnóstico em um serviço de nuvem, verifique se ConfigurationSettings especifica uma conta de armazenamento, como mostrado no seguinte exemplo:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores **AccountName** e **AccountKey** são encontrados no Portal de Gerenciamento do Microsoft Azure, no painel da conta de armazenamento, em Gerenciar Chaves de Acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois que a configuração de diagnóstico atualizada for aplicada ao serviço de nuvem e estiver gravando o diagnóstico no Armazenamento do Azure, você estará pronto para configurar o OMS.

## Habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS

Use o procedimento a seguir para habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o Portal de Gerenciamento do Microsoft Azure.

### Para habilitar os diagnósticos do Azure em uma máquina virtual com o Portal de Gerenciamento do Azure

1. Instale o Agente de VM quando criar uma máquina virtual. Se a máquina virtual já existir, verifique se o Agente de VM já está instalado.
	- Se você usar o portal de gerenciamento do Azure padrão para criar a máquina virtual, execute uma **Criação Personalizada** e selecione **Instalar o Agente de VM**.
	- Se você usar o novo portal de gerenciamento do Azure para criar uma máquina virtual, selecione **Configuração Opcional**, **Diagnóstico** e defina o **Status** como **Ativo**.

	Após a conclusão, a VM terá automaticamente a extensão de diagnóstico do Azure instalada e em execução, que será responsável por coletar os dados de diagnóstico.

2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e, em seguida, configurar o log de eventos, execute as seguintes etapas:
	1. Selecione a VM.
	2. Clique em **Monitoramento**.
	3. Clique em **Diagnóstico**.
	4. Defina o **Status** como **ATIVO**.
	5. Selecione cada métrica de diagnóstico que deseja usar. O OMS pode analisar logs do sistema de eventos do Windows, logs do aplicativo de eventos do Windows e logs do IIS.
	7. Clique em **OK**.

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure. Consulte o esquema de configuração do Diagnóstico do Azure 1.2 para um exemplo de arquivo de configuração e documentação detalhada sobre seu esquema. Certifique-se de instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior em [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Se você tiver uma versão do Diagnóstico do Microsoft Azure instalada anterior à versão 1.2, você não pode usar o novo portal para habilitar ou configurar o diagnóstico.

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


## Habilitar a análise do Armazenamento do Azure pelo OMS

É possível habilitar a análise de armazenamento e configurar o OMS para fazer a leitura da conta de armazenamento do Azure com o Diagnóstico do Azure usando as informações descritas em [Fontes de dados do Log Analytics](log-analytics-data-sources.md#collect-data-from-azure-diagnostics).

Em aproximadamente 1 hora, você começará a ver os dados da conta de armazenamento disponíveis para análise no OMS.


## Próximas etapas

- [Definir configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md) se sua organização usar um servidor proxy ou firewall para que os agentes possam se comunicar com o serviço do Log Analytics.

<!---HONumber=AcomDC_0504_2016-->