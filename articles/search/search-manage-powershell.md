---
title: Gerenciar o Azure Search com scripts do PowerShell | Microsoft Docs
description: Gerencie o serviço de Azure Search com scripts do PowerShell. Criar ou atualizar um serviço do Azure Search e gerenciar chaves de administração do Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: bae9e2dcb4320c1da4f1d8e3c6ad50ce90195544
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958560"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gerencie o serviço de Azure Search com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Este tópico descreve os comandos do PowerShell para executar muitas das tarefas de gerenciamento dos serviços do Azure Search. Vamos apresentar a criação de um serviço de pesquisa, como escaloná-lo e como gerenciar suas chaves de API.
Esses comandos são paralelos às opções de gerenciamento disponíveis na [API REST de Gerenciamento do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Pré-requisitos
* É necessário ter o Azure PowerShell 1.0 ou superior. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
* É necessário estar conectado à sua assinatura do Azure no PowerShell, conforme descrito abaixo.

Primeiro, faça logon no Azure com este comando:

    Connect-AzureRmAccount

Especifique o endereço de email de sua conta do Azure e sua senha no diálogo de logon do Microsoft Azure.

Como alternativa, é possível fazer [logon de forma não interativa com uma entidade de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Se tiver várias assinaturas do Azure, você precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandos para ajudá-lo a começar
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

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

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
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

    # View your query key
    $queryKey

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

## <a name="next-steps"></a>Próximas etapas
Agora que o serviço foi criado, você pode executar as próximas etapas: compilar um [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md) e, por fim, criar e gerenciar o seu próprio aplicativo de pesquisa que usa o Azure Search.

* [Criar um índice de Azure Search no portal do Azure](search-create-index-portal.md)
* [Consultar um índice do Azure Search usando o Search Explorer no portal do Azure](search-explorer.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Como usar o Azure Search no .NET](search-howto-dotnet-sdk.md)
* [Analisar o tráfego do Azure Search](search-traffic-analytics.md)

