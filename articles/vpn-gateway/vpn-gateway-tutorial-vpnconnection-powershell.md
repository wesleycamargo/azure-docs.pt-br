---
title: Criar e gerenciar conexões de VPN S2S do Manage Azure usando o PowerShell | Microsoft Docs
description: Tutorial - criar e gerenciar VMs Windows com o módulo do Azure PowerShell
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
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012927"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Criar e gerenciar VMs Windows com o módulo do Azure PowerShell

As conexões VPN S2S do Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial orienta na conexão dos ciclos de vida VPN S2S de IPsec, como criar e gerenciar uma conexão de VPN S2S. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma conexão VPN S2S
> * Atualizar a propriedade de conexão: chave pré-compartilhada, BGP, política IPsec/IKE
> * Adicionar mais conexões VPN
> * Excluir uma conexão VPN

O diagrama a seguir mostra a topologia para este tutorial:

![Diagrama de conexão VPN site a site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.3 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="requirements"></a>Requisitos

Concluir o primeiro tutorial: "[Criar gateway VPN com o Microsoft PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md)" para criar os seguintes recursos:

1. O grupo de recursos (TestRG1), a rede virtual (VNet1) e GatewaySubnet
2. Gateway VPN (VNet1GW)

Os valores de parâmetro de rede virtual estão listados abaixo. Observe os valores adicionais para o gateway de rede local representar sua rede local. Altere os valores com base na sua configuração de rede e do ambiente.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

O fluxo de trabalho para criar uma conexão VPN S2S é simples:

1. Criar um gateway de rede local para representar rede local
2. Criar uma conexão VPN do Microsoft Azure entre o gateway de VPN do Azure e o gateway de rede local

## <a name="create-a-local-network-gateway"></a>Criar um gateway de rede local

Criar um gateway de rede local para representar a sua rede local. Você pode especificar as propriedades de sua rede local no gateway de rede local, incluindo:

* Endereço IP público de seu gateway de VPN.
* Espaço de endereço local
* (Opcional) Atributos BGP (endereço IP do par BGP e número AS)

Criar um gateway de rede local com o comando [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Criar uma conexão VPN S2S

Em seguida, crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN com o [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Observe que o '-ConnectionType' para VPN Site a Site é *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Adicionar a propriedade opcional "**- EnableBGP $True**" para habilitar BGP para a conexão, se você estiver usando o BGP. Isso está desabilitado por padrão.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Atualizar a chave pré-compartilhada de conexão VPN, BGP, política IPsec/IKE

### <a name="view-and-update-your-pre-shared-key"></a>Exibir e atualizar a chave pré-compartilhada

A conexão de VPN S2S do Azure usa uma chave pré-compartilhada (secreta) para autenticar entre o dispositivo VPN no local e o gateway de VPN do Microsoft Azure. Você pode exibir e atualizar a chave pré-compartilhada para uma conexão com [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) e [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> A chave pré-compartilhada é uma cadeia de caracteres de **caracteres ASCII imprimíveis** não mais de 128 de comprimento.

Este comando mostra a chave pré-compartilhada para a conexão:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

A saída será "**Azure@!b2C3**" seguindo o exemplo acima. Use o comando a seguir para alterar o valor da chave pré-compartilhada para "**Azure@!_b2=C3**":

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Habilitar o BGP em conexão de VPN

O Gateway de VPN do Microsoft Azure oferece suporte ao protocolo de roteamento BGP. Você pode habilitar o BGP em cada conexão individual, dependendo se você estiver usando o BGP em seus dispositivos e redes locais. Especifique as seguintes propriedades BGP antes de habilitar o BGP na conexão:

* Azure VPN ASN (Número de Sistema Autônomo)
* Gateway de rede loca ASN
* Gateway de rede local BGP par de endereço IP

Se você não configurou as propriedades BGP, use os seguintes comandos para adicionar essas propriedades para o gateway VPN e o gateway de rede local: [conjunto Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) e [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Habilitar BGP com [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Você pode desabilitar BGP pela alteração do valor de propriedade "-EnableBGP" para **$False** Consulte em [de BGP em gateways de VPN do Azure](vpn-gateway-bgp-resource-manager-ps.md) para obter mais explicações sobre BGP em gateways de VPN do Azure.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Aplicar uma política personalizada da política IPsec/IKE na conexão

Você pode aplicar uma política de IPsec/IKE opcional para especificar a combinação exata de algoritmos de criptografia IPsec/IKE e restrições de chave de conexão, em vez de usar as [propostas padrão](vpn-gateway-about-vpn-devices.md#ipsec). O script de exemplo a seguir cria uma política de IPsec/IKE diferente com os parâmetros e os algoritmos seguintes:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, SA Tempo de vida 14.400 segundos & 102,400,000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Consulte [política IPsec/IKE para conexões S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter uma lista de algoritmos e instruções.

## <a name="add-another-s2s-vpn-connection"></a>Adicionar outra conexão VPN S2S

Para adicionar uma conexão de VPN S2S adicional ao mesmo gateway VPN, crie outro gateway de rede local e crie uma nova conexão entre o novo gateway de rede local e o gateway VPN. Seguindo o exemplo deste artigo.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Agora há duas conexões VPN S2S para o gateway de VPN do Azure.

![Conexões VPN de multissite](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Excluir uma conexão VPN S2S

Excluir uma conexão VPN S2S com [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Excluir o gateway de rede local se você não precisar mais. Você não pode excluir um gateway de rede local se houver outras conexões associadas a ele.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar e gerenciar conexões VPN S2S por exemplo, como:

> [!div class="checklist"]
> * Criar uma conexão VPN S2S
> * Atualizar a propriedade de conexão: chave pré-compartilhada, BGP, política IPsec/IKE
> * Adicionar mais conexões VPN
> * Excluir uma conexão VPN

Ir para os tutoriais a seguir para saber mais sobre conexões de P2S, S2S e VNet para VNet.

> [!div class="nextstepaction"]
> * [Criar conexões VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar conexões P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
