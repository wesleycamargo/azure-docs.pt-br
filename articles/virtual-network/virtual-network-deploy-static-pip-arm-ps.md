---
title: Criar uma VM com um endereço IP público estático - PowerShell | Microsoft Docs
description: Saiba como criar uma VM com um endereço IP público estático usando o PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: f5ddc4a85148cee3e8c8b4d2bf1955f233ebdbc1
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426515"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Crie uma máquina virtual com um endereço IP público estático usando o PowerShell

Você pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite que você se comunique com uma máquina virtual pela Internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado. Saiba mais sobre os [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm [carga nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Você pode concluir as etapas a seguir no seu computador local ou usando o Shell de Nuvem do Azure. Para usar seu computador local, verifique se você tem o [Azure PowerShell instalado](/powershell/azure/azurerm/install-azurerm-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para usar o Azure Cloud Shell, selecione **Experimente** no canto superior direito de qualquer caixa de comando a seguir. O Cloud Shell entrará no Azure.

1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `Connect-AzureRmAccount`.
2. Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

   ```azurepowershell-interactive
   New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Crie uma máquina virtual com o comando [New-AzureRmVM](/powershell/module/AzureRM.Compute/New-AzureRmVM). O `-AllocationMethod "Static"` opção atribui um endereço IP público estático para a máquina virtual. O exemplo a seguir cria uma máquina virtual do Windows Server com um endereço IP público, estático e básico da SKU denominado *myPublicIpAddress*. Quando solicitado, forneça um nome de usuário e uma senha para serem usados como as credenciais de login da máquina virtual:

   ```azurepowershell-interactive
   New-AzureRmVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se o endereço IP público precisar ser um SKU padrão, você deverá [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address), [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface), [atribuir o endereço IP público à interface de rede](virtual-network-network-interface-addresses.md#add-ip-addresses) e, em seguida, [crie uma máquina virtual com a interface de rede](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), em etapas separadas. Saiba mais sobre [endereço IP público SKUs](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um Azure Load Balancer público, o SKU do endereço IP público da máquina virtual deverá corresponder ao SKU do endereço IP público do balanceador de carga. Para obter detalhes, consulte [balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Visualize o endereço IP público atribuído e confirme que ele foi criado como um endereço estático, com [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

   ```azurepowershell-interactive
   Get-AzureRmPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   O Azure atribuiu um endereço IP público dos endereços usados na região na qual você criou a máquina virtual. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
Não modifique as configurações do endereço IP no sistema operacional da máquina virtual. O sistema operacional fica ciente dos endereços IP públicos do Azure. Embora você possa adicionar configurações de endereço IP privado ao sistema operacional, recomendamos não fazê-lo, a menos que seja necessário, e somente depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todos os [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuir uma [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre como criar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais