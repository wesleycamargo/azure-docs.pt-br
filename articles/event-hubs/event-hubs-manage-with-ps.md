---
title: Usar o PowerShell para gerenciar recursos dos Hubs de Eventos do Azure | Microsoft Docs
description: "Usar o módulo do PowerShell para criar e gerenciar Hubs de Eventos"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a3ba385e15510139929735adb5e50b6291846356
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Usar o PowerShell para gerenciar recursos dos Hubs de Eventos

O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento dos serviços do Azure. Este artigo descreve como usar o [Módulo do PowerShell do Gerenciador de Recursos dos Hubs de Eventos](/powershell/module/azurerm.eventhub) para provisionar e gerenciar entidades dos Hubs de Eventos (grupos de consumidor, namespaces e Hubs de Eventos) usando um script ou console local do Azure PowerShell.

Também é possível gerenciar recursos dos Hubs de Eventos usando modelos do Azure Resource Manager. Para obter mais informações, consulte o artigo [Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma assinatura do Azure. Para saber mais sobre como adquirir uma assinatura, confira [Opções de compra][purchase options], [ofertas para membros][member offers] ou [conta gratuita][free account].
* Um computador com o PowerShell do Azure. Para obter instruções, consulte [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="get-started"></a>Introdução

A primeira etapa é usar o PowerShell para fazer logon em sua conta do Azure e assinatura do Azure. Siga as instruções contidas em [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para fazer logon em sua conta do Azure e recuperar e acessar os recursos em sua assinatura do Azure.

## <a name="provision-an-event-hubs-namespace"></a>Provisionar um namespace dos Hubs de Eventos

Ao trabalhar com namespaces dos Hubs de Eventos, você pode usar os cmdlets [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) e [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

* `$Namespace` é o nome do namespace dos Hubs de Eventos com que queremos trabalhar.
* `$Location` identifica o datacenter em que iremos provisionar o namespace.
* `$CurrentNamespace` armazena o namespace de referência que recuperamos (ou criamos).

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Esta parte do script tem a seguinte função:

1. Tenta recuperar um namespace dos Hubs de Eventos com o nome especificado.
2. Se o namespace for encontrado, ele informará o que foi encontrado.
3. Se o namespace não for encontrado, ele vai criar o namespace e, em seguida, recuperar o namespace recentemente criado.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar um Hub de Eventos, execute uma verificação de namespace usando o script na seção anterior. Em seguida, use o cmdlet New-[AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) para criar o Hub de Eventos:

```powershell
# Check if Event Hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The Event Hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName Event Hub does not exist."
    Write-Host "Creating the $EventHubName Event Hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName Event Hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Para criar um grupo de consumidores dentro de um Hub de Eventos, execute as verificações do namespace e do Hub de Eventos usando os scripts na seção anterior. Em seguida, use o cmdlet [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) para criar o grupo de consumidores no Hub de Eventos. Por exemplo:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in Event Hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in Event Hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Definir metadados do usuário

Após executar os scripts nas seções anteriores, você pode usar o cmdlet [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) para atualizar as propriedades de um grupo de consumidores, como no exemplo a seguir:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Remover Hub de Eventos

Para remover as entidades dos Hubs de Eventos que foram criadas, você pode usar os cmdlets `Remove-*`, como no exemplo a seguir:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Próximas etapas

- Consulte a documentação completa sobre o módulo do PowerShell do Gerenciador de Recursos dos Hubs de Eventos [aqui](/powershell/module/azurerm.eventhub). Esta página lista todos os cmdlets disponíveis.
- Para obter informações sobre como usar modelos do Azure Resource Manager, consulte o artigo [Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informações sobre [Bibliotecas de gerenciamento .NET dos Hubs de Eventos](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/

