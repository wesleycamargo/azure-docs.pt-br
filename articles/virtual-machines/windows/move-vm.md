---
title: "Mover um recurso de máquina virtual do Windows no Azure | Microsoft Docs"
description: "Mova uma VM Windows para outro grupo de recursos ou outra assinatura do Azure no modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: f4b739fd34cc0c85d47b97b7b42a70eb7f5f5ac7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM Windows para outra assinatura ou outro grupo de recursos do Azure
Este artigo explica como mover uma VM Windows entre grupos de recursos ou assinaturas. A movimentação entre assinaturas poderá ser útil se você tiver originalmente criado uma VM em uma assinatura pessoal e agora deseja movê-la para a assinatura da sua empresa a fim de continuar seu trabalho.

> [!IMPORTANT]
>No momento, não é possível mover o Managed Disks. 
>
>Novas IDs de recurso são criadas como parte da mudança. Após a mudança da VM, você precisa atualizar ferramentas e scripts para usar as novas IDs de recurso. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Usar o Powershell para mover uma VM

Para mover uma máquina virtual para outro grupo de recursos, você precisa ter certeza de também mover todos os recursos dependentes. Para usar o cmdlet Move-AzureRMResource, você precisa do ResourceId de cada um dos recursos. Você pode obter uma lista do ResourceId usando o cmdlet [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Para mover uma VM, precisamos mover vários recursos. Podemos usar a saída de Find-AzureRMResource para criar uma lista separada por vírgulas de ResourceIds e passá-la para [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) para movê-la para o destino. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Para mover os recursos para outra assinatura, inclua o parâmetro **-DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Será solicitado que você confirme se deseja mover os recursos especificados. 

## <a name="next-steps"></a>Próximas etapas
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).    

