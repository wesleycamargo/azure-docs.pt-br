<properties 
	pageTitle="Gerenciar a Pesquisa do Azure com scripts do PowerShell | Microsoft Azure | Serviço de pesquisa de nuvem hospedada" 
	description="Gerencie o serviço da Pesquisa do Azure no Microsoft Azure com scripts do PowerShell. Criar ou atualizar um serviço da Pesquisa do Azure e gerenciar chaves de administração da Pesquisa do Azure"  
	services="search" 
	documentationCenter="" 
	authors="seansaleh" 
	manager="mblythe" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="powershell" 
	ms.date="02/25/2016" 
	ms.author="seasa"/>

# Gerenciar seu serviço da Pesquisa no Microsoft Azure com o PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Este tópico descreve os comandos do PowerShell para executar muitas das tarefas de gerenciamento dos serviços da Pesquisa do Azure. Vamos apresentar a criação de um serviço de pesquisa, como escaloná-lo e como gerenciar suas chaves de API. Esses comandos são paralelos às opções de gerenciamento disponíveis na [API REST de Gerenciamento da Pesquisa do Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## Pré-requisitos
 
- É necessário ter o Azure PowerShell 1.0 ou superior. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
- É necessário estar conectado à sua assinatura do Azure no PowerShell, conforme descrito abaixo.

Primeiro, faça logon no Azure com este comando:

	Login-AzureRmAccount

Especifique o endereço de email de sua conta do Azure e sua senha no diálogo de logon do Microsoft Azure.

Como alternativa, é possível fazer [logon de forma não interativa com uma entidade de serviço](../resource-group-authenticate-service-principal.md).

Se tiver várias assinaturas do Azure, você precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## Comandos para ajudá-lo a começar

	$serviceName = "your-service-name-lowercase-with-dashes"
	$sku = "free" # or "standard" for a paid service
	$location = "West US"
	# You can get a list of potential locations with
	# (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
	$resourceGroupName = "YourResourceGroup" 
	# If you don't already have this resource group, you can create it with with 
	# New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

	# Register the arm provider idempotently. This must be done once per subscription
	Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

	# Create a new search service
	# This command will return once the service is fully created
	New-AzureRmResourceGroupDeployment `
		-ResourceGroupName $resourceGroupName `
		-TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
		-NameFromTemplate $serviceName `
		-Sku $sku `
		-Location $location `
		-PartitionCount 1 `
		-ReplicaCount 1
	
	# Get information about your new service and store it in $resource
	$resource = Get-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
	
	# View your resource
	$resource
	
	# Get the primary admin api key
	$primaryKey = (Invoke-AzureRmResourceAction `
		-Action listAdminKeys `
		-ResourceId ($resource.ResourceId) `
		-ApiVersion 2015-08-19).PrimaryKey

	# Regenerate the secondary admin api Key
	$secondaryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action secondary).SecondaryKey

	# Create a query key for read only access to your indexes
	$queryKeyDescription = "query-key-created-from-powershell"
	$queryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/createQueryKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action $queryKeyDescription).Key

	# Delete query key
	Remove-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
		
	# Scale your service up
	# Note that this will only work if you made a non "free" service
	# This command will not return until the operation is finished
	# It can take 15 minutes or more to provision the additional resources
	$resource.Properties.ReplicaCount = 2
	$resource | Set-AzureRmResource
	
	# Delete your service
	# Deleting your service will delete all indexes and data in the service
	$resource | Remove-AzureRmResource
	
## Próximas etapas
	
Agora que o serviço foi criado, é possível executar as próximas etapas: compilar um [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md) e, por fim, criar e gerenciar seu próprio aplicativo de pesquisa que usa a Pesquisa do Azure.

- [Criar um índice de Pesquisa do Azure no Portal do Azure](search-create-index-portal.md)

- [Consultar um índice de Pesquisa do Azure usando o Gerenciador de Pesquisa no Portal do Azure](search-explorer.md)

- [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)

- [Como usar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md)

- [Analisar o tráfego da Pesquisa do Azure](search-traffic-analytics.md)

<!---HONumber=AcomDC_0302_2016-->