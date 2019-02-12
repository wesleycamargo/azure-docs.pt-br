---
title: Exemplo de script do Azure PowerShell – Criar um Gateway de VPN | Microsoft Docs
description: Crie um Gateway de VPN usando PowerShell.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: c1b5a164f477887c68a8988f3ea4ca1dc20306eb
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700512"
---
# <a name="create-a-vpn-gateway-with-powershell"></a>Criar um Gateway de VPN usando PowerShell

Este script cria um Gateway de VPN baseado em rota.


```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzureRmVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzureRmVirtualNetwork
# Request a public IP address
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos. Isso excluirá o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede. Essa configuração é usada com o processo de criação de rede virtual. |
| [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) | Obtém detalhes de uma rede virtual. |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Essa configuração é usada com o processo de criação de rede virtual. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público. |
|[New-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgateway?view=azurermps-6.8.1) | Cria um gateway de VPN. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Define a configuração de sub-rede da rede virtual. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).
