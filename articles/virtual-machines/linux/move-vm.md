---
title: Mover uma VM do Linux no Azure | Microsoft Docs
description: "Mova uma VM Linux para outro grupo de recursos ou outra assinatura do Azure no modelo de implantação do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM Linux para outro grupo de recursos ou outra assinatura
Este artigo explica como mover uma VM Linux entre grupos de recursos ou assinaturas. Mover uma VM entre assinaturas poderá ser útil se você tiver criado uma VM em uma assinatura pessoal e agora quiser movê-la para a assinatura da sua empresa.

> [!IMPORTANT]
>No momento, não é possível mover o Managed Disks. 
>
>Novas IDs de recurso são criadas como parte da mudança. Após a mudança da VM, você precisa atualizar ferramentas e scripts para usar as novas IDs de recurso. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar a CLI do Azure para mover uma VM


Antes de você pode mover sua VM usando a CLI, você precisa ter certeza de que as assinaturas de origem e de destino existam dentro do mesmo locatário. Para verificar se as duas assinaturas têm a mesma ID de locatário, use [az account show](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se as IDs do locatário para as assinaturas de origem e de destino não forem iguais, entre em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover os recursos para um novo locatário.

Para mover uma VM com êxito, você precisa mover a VM e todos os seus recursos de suporte. Use o comando [az resource list](/cli/azure/resource#az_resource_list) para listar todos os recursos em um grupo de recursos e suas IDs. Ele ajuda a direcionar a saída desse comando para um arquivo de modo que seja possível copiar e colar as IDs em comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Para mover uma VM e seus recursos para outro grupo de recursos, use [az resource move](/cli/azure/resource#az_resource_move). O exemplo a seguir mostra como mover uma VM e os recursos mais comuns que ela exige. Use o parâmetro **-ids** e passe-o em uma lista de IDs separadas por vírgulas (sem espaços) para os recursos a serem movidos.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Se quiser mover a VM e seus recursos para outra assinatura, adicione o parâmetro **--destination-subscriptionId** para especificar a assinatura de destino.

Se você receber uma solicitação para confirmar que deseja mover o recurso especificado. Digite **Y** para confirmar que deseja mover os recursos.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).    

