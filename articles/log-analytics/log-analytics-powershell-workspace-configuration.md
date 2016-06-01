<properties
	pageTitle="Usar o PowerShell para criar e configurar um espaço de trabalho do Log Analytics | Microsoft Azure"
	description="O Log Analytics usa dados de servidores em sua infraestrutura local ou na nuvem. Você pode coletar dados da máquina do armazenamento do Azure quando gerados pelo diagnóstico do Azure."
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# Gerenciar o Log Analytics usando o PowerShell

Você pode usar os [cmdlets do PowerShell do Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para executar uma variedade de funções no Log Analytics de uma linha de comando ou como parte de um script. Os exemplos das tarefas que você pode executar com o PowerShell incluem:

+ Criar um espaço de trabalho
+ Adicionar ou remover uma solução
+ Importar e exportar pesquisas salvas
+ Adicionar o agente de análise de log a uma máquina virtual do Azure
+ Configurar a análise de log para indexar os dados coletados usando o diagnóstico do Azure

Este artigo fornece dois exemplos de código que ilustram algumas das funções que podem ser executadas do PowerShell. Você pode consultar a [referência do cmdlet do PowerShell do Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para outras funções.

> [AZURE.NOTE] O Log Analytics chamava-se Operational Insights, e é por isso que esse é o nome usado nos cmdlets.

## Pré-requisitos

Para usar o PowerShell com seu espaço do Log Analytics, você deverá ter:

+ Uma assinatura do Azure e 
+ Seu espaço de trabalho do Log Analytics vinculado à sua assinatura do Azure.

Se você tiver criado um espaço de trabalho do OMS, mas ainda não o tiver vinculado a uma assinatura do Azure, poderá criar o link no portal do Azure, no portal do OMS ou usando os cmdlets AzureRMOperationalInsightsLinkTargets e New-AzureRMOperationalInsightsWorkspace.

## Criar um Espaço de Trabalho do Log Analytics, adicionar soluções e pesquisas salvas

O exemplo de script a seguir ilustra como:

1.	Criar um espaço de trabalho
2.	Listar as soluções disponíveis
3.	Adicionar soluções ao espaço de trabalho
4.	Importar pesquisas salvas
5.	Exportar pesquisas salvas

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Configuração do Log Analytics para indexar os diagnósticos do Azure 

Para o monitoramento sem agente de recursos do Azure, incluindo como funções Web e funções de trabalho, clusters do service fabric, grupos de segurança de rede, cofres de chaves e gateways de aplicativo, primeiro os recursos precisam ter o diagnóstico do Azure habilitado para gravar em uma conta de armazenamento e então o Log Analytics poderá ser configurado para coletar os logs da conta de armazenamento.

Você também pode usar o PowerShell para configurar um espaço de trabalho do Log Analytics em uma assinatura do Azure para coletar logs de assinaturas do Azure diferentes.

O exemplo a seguir mostra como:

1.	Listar as contas de armazenamento existentes e os locais de onde o Log Analytics indexará os dados
2.	Criar uma nova configuração para ler de uma conta de armazenamento
3.	Atualizar a configuração criada recentemente para indexar dados de locais adicionais
4.	Excluir a configuração recém-criada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## Próximas etapas

- [Examinar cmdlets do PowerShell do Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para obter informações adicionais sobre como usar o PowerShell para a configuração do Log Analytics.

<!---HONumber=AcomDC_0518_2016-->