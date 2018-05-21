---
title: Criar e gerenciar o gateway de VPN do Microsoft Azure usando o PowerShell | Microsoft Docs
description: Tutorial - Criar e gerenciar o gateway de VPN com o módulo Microsoft Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 867a1c55c0f96b17f77049d7f24d47f41a90d9f9
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Criar e gerenciar o gateway de VPN com o módulo Microsoft Azure PowerShell

As conexões VPN do Microsoft Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial aborda itens básicos da implantação de gateway VPN do Azure como criar e gerenciar um gateway de VPN. Você aprenderá como:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Redimensionar um gateway VPN
> * Redefinir um gateway de VPN

O diagrama a seguir mostra a rede virtual e o gateway da VPN criados como parte deste tutorial.

![Gateway de VNet e VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)


[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.3 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="common-network-parameter-values"></a>Valores de parâmetros de rede comuns

Altere os valores abaixo com base na sua configuração de rede e do ambiente.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Um grupo de recursos deve ser criado primeiro. No exemplo a seguir, um grupo de recursos chamado *TestRG1* é criado na região *Leste dos EUA*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

O gateway VPN do Azure fornece conectividade entre locais e a funcionalidade do servidor VPN de P2S para sua rede virtual. Adicione o gateway VPN a uma rede virtual existente ou crie uma nova rede virtual e o gateway. Este exemplo cria uma nova rede virtual com três sub-redes: GatewaySubnet de front-end e back-end usando [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) e [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Solicitar um endereço IP público para o gateway de VPN

Os gateways VPN do Azure se comunicam com os dispositivos VPN local pela Internet para realizar a negociação de IKE (Internet Key Exchange) e estabelecer os túneis IPsec. Criar e atribuir um endereço IP público para o gateway VPN, conforme mostrado no exemplo a seguir com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) e [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> No momento, você pode atribuir apenas um endereço IP público dinâmico a um gateway. Não há suporte para o endereço IP estático em gateways de VPN do Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Criar um gateway de VPN

Pode demorar 45 minutos ou mais para que o gateway VPN seja criado. Uma vez que o gateway tenha sido concluído, você pode criar uma conexão entre sua rede virtual e outra rede virtual. Ou criar uma conexão entre sua rede virtual e as redes locais. Criar um gateway VPN usando o cmdlet [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valores de parâmetros de execução:
* GatewayType: usar o **Vpn** para conexões site a site e VNet a VNet
* VpnType: use **RouteBased** para interagir com dispositivos mais largos do intervalo de VPN e mais recursos de roteamento
* GatewaySku: **VpnGw1** é o padrão; altere-o para VpnGw2 ou VpnGw3 se precisar de taxas de transferência mais altas ou mais conexões. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Concluída a criação de gateway, crie uma conexão entre a rede virtual e outro VNet ou crie uma conexão entre a rede virtual e um local. Você também pode configurar uma conexão P2S para sua rede virtual em um computador cliente.

## <a name="resize-vpn-gateway"></a>Redimensionar o VPN do Azure

Você poderá alterar a SKU de gateway do VPN depois que o gateway for criado. Especificações diferentes de suporte de SKUs de gateway como taxas de transferência, número de conexões etc. O exemplo a seguir usa [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) para redimensionar o gateway de VpnGw1 para VpnGw2. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Redimensionar um gateway VPN também leva cerca de 30 a 45 minutos, embora esta operação **não** interrompa ou remove as conexões existentes e as configurações.

## <a name="reset-vpn-gateway"></a>Redefinir Gateway de VPN

Como parte das etapas de solução de problemas, é possível redefinir seu gateway de VPN do Azure para forçar o gateway VPN para reiniciar as configurações de túnel IPsec/IKE. Use [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) para redefinir seu gateway.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Para obter mais informações, consulte [Redefinir um gateway VPN](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Obter o endereço IP público do gateway

Se você souber o nome do endereço IP público, use [Get-AzureRmPublicIpAddresst](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) para mostrar o endereço IP público atribuído ao gateway.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Excluir um gateway de VPN

Uma configuração completa entre instalações e conectividade VNet a VNet requer vários tipos de recurso além do gateway VPN. Exclua as conexões associadas ao gateway VPN antes de excluir o próprio gateway. Depois que o gateway for excluído, você pode excluir os endereços IP públicos para o gateway. Consulte [excluir um gateway VPN](vpn-gateway-delete-vnet-gateway-powershell.md) para obter as etapas detalhadas.

Se o gateway for parte de uma implantação de protótipo ou prova de conceito, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) de comando para remover o grupo de recursos, o gateway VPN e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação do gateway VPN e o gerenciamento como:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Redimensionar um gateway VPN
> * Redefinir um gateway de VPN

Ir para os tutoriais a seguir para saber mais sobre conexões de P2S, S2S e VNet para VNet.

> [!div class="nextstepaction"]
> * [Criar Conexões S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Criar conexões VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar conexões P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
