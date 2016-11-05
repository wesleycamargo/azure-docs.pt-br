---
title: Analisar logs de diagnóstico do Azure usando o Log Analytics | Microsoft Docs
description: O Log Analytics consegue ler os logs de serviços do Azure que gravam logs de diagnóstico do Azure no armazenamento de blobs no formato JSON.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analisar logs de diagnóstico do Azure usando o Log Analytics
O Log Analytics consegue coletar os logs para os seguintes serviços do Azure que gravam [logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) no armazenamento de blobs no formato JSON:

* Automação (Preview)
* Cofre de Chaves (Preview)
* Application Gateway (Preview)
* Grupo de Segurança de Rede (Preview)

As seções a seguir guiarão você pelo uso do PowerShell para:

* Configurar o Log Analytics para coletar os logs do armazenamento para cada recurso  
* Habilitar a solução do Log Analytics para o serviço do Azure

Antes que o Log Analytics possa coletar dados para esses recursos, o diagnóstico do Azure deve ser habilitado. Use o cmdlet `Set-AzureRmDiagnosticSetting` para habilitar o registro em log.

Consulte os seguintes artigos para obter mais informações sobre como criar alertas:

* [Cofre da Chave](../key-vault/key-vault-logging.md)
* [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
* [Grupo de Segurança de Rede](../virtual-network/virtual-network-nsg-manage-log.md)

Esta documentação também inclui detalhes sobre:

* Solucionar problemas de coleta de dados
* Interromper a coleta de dados

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar o Log Analytics para coletar logs de diagnóstico do Azure
A coleta de logs para esses serviços e a habilitação da solução para visualizar os logs é executada usando scripts do PowerShell.

O exemplo a seguir habilita o registro em todos os recursos com suporte

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Para alguns recursos, é possível executar a configuração anterior do portal do Azure usando as etapas descritas em [Visão geral dos logs de diagnóstico de do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar o Log Analytics para coletar logs de diagnóstico do Azure
Nós fornecemos um módulo de script do PowerShell que exporta dois cmdlets para ajudá-lo a configurar o Log Analytics:

1. `Add-AzureDiagnosticsToLogAnalyticsUI` solicita a entrada e é capaz de definir configurações simples
2. `Add-AzureDiagnosticsToLogAnalytics` usa os recursos para monitorar como entrada e, em seguida, configura o Log Analytics  

### <a name="pre-requisites"></a>Pré-requisitos
1. Azure PowerShell com versão 1.0.8 ou mais recente dos cmdlets do Insights Operacionais.
   * [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
   * Para verificar a sua versão dos cmdlets: `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. O log de diagnósticos está configurado para o recurso do Azure que você deseja monitorar. Use `Set-AzureRmDiagnosticSetting` ou consulte [Usar o Log Analytics para coletar dados de contas de armazenamento do Azure](log-analytics-azure-storage.md) para saber como habilitar o diagnóstico.
3. Um espaço de trabalho do [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. O módulo AzureDiagnosticsAndLogAnalytics do PowerShell
   * Baixe módulo o [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) da Galeria do PowerShell

### <a name="option-1:-run-the-interactive-configuration-scripts"></a>Opção 1: executar os scripts de configuração interativa
Abra o PowerShell e execute:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Uma lista de seleções disponíveis é mostrada a você, seguida de um prompt para fazer sua seleção.
É necessário fazer seleções para cada um dos seguintes itens:

* Tipos de recursos (Serviços do Azure) dos quais coletar logs
* Instâncias de recursos dos quais coletar logs
* Espaço de trabalho do Log Analytics para coletar os dados

Após executar esse script, você deve ver registros no Log Analytics cerca de 30 minutos após a gravação dos novos dados de diagnóstico no armazenamento. Se os registros não estiverem disponíveis após esse tempo, consulte a seção de solução de problemas abaixo.

### <a name="option-2:-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opção 2: criar uma lista de recursos e passá-los para o cmdlet de configuração
Você pode criar uma lista de recursos que têm o diagnóstico do Azure habilitado e, em seguida, passar os recursos para o cmdlet de configuração.

Você pode ver informações adicionais sobre o cmdlet executando `Get-Help Add-AzureDiagnosticsToLogAnalytics`.

Para obter mais detalhes, consulte os [Cmdlets do PowerShell do Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx)

> [!NOTE]
> Se o espaço de trabalho e os recursos estiverem em diferentes assinaturas do Azure, alterne entre eles, conforme necessário usando `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`
> 
> 

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Após executar esse script, você deve ver registros no Log Analytics cerca de 30 minutos após a gravação dos novos dados de diagnóstico no armazenamento. Se os registros não estiverem disponíveis após esse tempo, consulte a seção de solução de problemas abaixo.  

> [!NOTE]
> A configuração não está visível no portal do Azure. Você pode verificar a configuração usando o cmdlet `Get-AzureRmOperationalInsightsStorageInsight` .  
> 
> 

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Impedir o Log Analytics de coletar logs de diagnóstico do Azure
Para excluir a configuração do Log Analytics para um recurso, use o cmdlet `Remove-AzureRmOperationalInsightsStorageInsight`.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Solucionar problemas de configuração para logs de diagnóstico do Azure
*Problema*

O seguinte erro é exibido ao configurar um recurso que está fazendo logon em armazenamento clássico:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Resolução*

Faça logon no API para o modelo de implantação clássico com `Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Solucionar problemas de coleta de dados para logs de diagnóstico do Azure
Se você não estiver vendo dados para o recurso do Azure no Log Analytics, você poderá usar as seguintes etapas de solução de problemas:

* Verificar os dados que fluem para a conta de armazenamento
* Verificar se a solução do Log Analytics para o serviço está habilitada
* Verificar se o Log Analytics está configurado para ler do armazenamento
* Atualizar a chave de conta de armazenamento

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Verificar se os dados estão fluindo para a conta de armazenamento
Você pode verificar se os dados estão fluindo para a conta de armazenamento com uma ferramenta que permite a navegação no armazenamento do Azure (por exemplo, Visual Studio) ou usando o PowerShell.

Para localizar a Conta de Armazenamento, o recurso é configurado para que o log use o PowerShell a seguir:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

O contêiner de armazenamento usado pelo diagnóstico do Azure tem um nome com um formato de:  

`insights-logs-<Category>`

Consulte [Usando cmdlets de armazenamento para verificar um contêiner para dados recentes](../storage/storage-powershell-guide-full.md) para saber mais sobre como exibir o conteúdo da conta de armazenamento.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Verificar se a solução do Log Analytics para o serviço está habilitada
Se você usar `Add-AzureDiagnosticsToLogAnalyticsUI` , a solução correta do Log Analytics será habilitada automaticamente para você.

Para verificar se uma solução está habilitada, execute o PowerShell a seguir:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Se a solução não estiver habilitada, você poderá habilitá-la usando o PowerShell a seguir:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Para localizar o nome da solução a habilitar para cada tipo de recurso, use o PowerShell a seguir (essa variável fica disponível após você ter importado o módulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Verificar se o Log Analytics está configurado para ler do armazenamento
Se você adicionar mais Recursos do Azure, você precisará habilitar o log de diagnóstico e configurar o Log Analytics para eles.
Para verificar para quais recursos e contas de armazenamento o Log Analytics está configurado para coletar, use o PowerShell a seguir:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Atualizar a Chave de Armazenamento
Se você altera a chave de conta de armazenamento, a configuração do Log Analytics também precisa ser atualizada com a nova chave.
Você pode atualizar a configuração do Log Analytics com uma nova chave usando o PowerShell a seguir:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Para localizar o nome do insight de armazenamento, use o cmdlet `Get-AzureRmOperationalInsightsStorageInsight` , conforme mostrado nos exemplos anteriores.

## <a name="next-steps"></a>Próximas etapas
* [Use o armazenamento de blobs para IIS e armazenamento de tabelas para eventos](log-analytics-azure-storage-iis-table.md) para ler os logs de serviços do Azure que gravam diagnósticos em armazenamento de tabelas ou de logs do IIS gravados para armazenamento de blobs.
* [Habilitar Soluções](log-analytics-add-solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.

<!--HONumber=Oct16_HO2-->


