---
title: "Sobre a configuração do dispositivo VPN de terceiros para conectar-se a gateways de VPN do Azure | Microsoft Docs"
description: "Este artigo apresenta uma visão geral das configurações de dispositivo VPN de terceiros para conexão a gateways de VPN do Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: pt-br
ms.lasthandoff: 07/10/2017


---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>Visão geral das configurações de dispositivo VPN de terceiros
Este artigo apresenta uma visão geral das configurações de dispositivo VPN local para conexão a gateways de VPN do Azure. O exemplo de rede virtual do Azure e a instalação do gateway de VPN serão usados para conectar diferentes dispositivos VPN locais com os mesmos parâmetros.

## <a name="device-requirements"></a>Requisitos do dispositivo
Gateways VPN do Azure usam conjuntos padrão de protocolo IPsec/IKE para túneis VPN S2S. Consulte [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) para os parâmetros detalhados de protocolo IPsec/IKE e algoritmos de criptografia padrão para gateways VPN do Azure. Opcionalmente, você pode especificar a combinação exata de algoritmos de criptografia e forças de chave para uma conexão específica, conforme descrito em [Sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Túnel VPN único
A primeira topologia consiste em um único túnel VPN S2S entre um gateway de VPN do Azure e o dispositivo VPN local. Opcionalmente, você pode configurar o BGP por meio do túnel VPN.

![túnel único](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Consulte [Configurar conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) para obter diretrizes passo a passo detalhadas. As seções a seguir listam os parâmetros e dão um exemplo de script do PowerShell para ajudá-lo a começar.

### <a name="network-and-vpn-gateway-information"></a>Informações de gateway de VPN e de rede
Esta seção lista os parâmetros para os exemplos anteriores.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereços da VNET        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway de VPN do Azure         | IP do Gateway de VPN do Azure         |
| Prefixos de endereço local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN local    | IP do dispositivo VPN local    |
| *ASN de BGP da VNet                | 65010                        |
| *IP de par no nível de protocolo BGP do Azure           | 10.12.255.30                 |
| * ASN de BGP local         | 65050                        |
| * IP de par no nível de protocolo BGP local     | 10.52.255.254                |

* (*) Parâmetros opcionais apenas para o BGP

### <a name="sample-powershell-script"></a>Exemplo de script do PowerShell
[Criar uma conexão VPN S2S usando o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) apresenta instruções detalhadas. Esta seção apresenta um exemplo de script para você começar.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>[Opcional] Usar a política IPsec/IKE personalizada com "UsePolicyBasedTrafficSelectors"
Se os dispositivos VPN não derem suporte a seletores de tráfego "qualquer a qualquer" (configuração baseada em rota/baseada em VTI), você precisará criar uma política personalizada de IPsec/IKE e configurar a opção "UsePolicyBasedTrafficSelectors" conforme descrito em [neste artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> Você precisa criar uma política de IPsec/IKE para habilitar a opção "UsePolicyBasedTrafficSelectors" na conexão.

O script de exemplo a seguir cria uma política de IPsec/IKE com os parâmetros e os algoritmos seguintes:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Tempo de vida 7.200 segundos e 20.480.000 KB (20 GB)

Então ele aplica a política e habilita a conexão "UesPolicyBasedTrafficSelectors".

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[Opcional] Usar BGP na conexão VPN S2S
Opcionalmente, você pode usar o BGP na conexão. Consulte [BGP para gateway de VPN](vpn-gateway-bgp-resource-manager-ps.md). Há duas diferenças:

Os prefixos de endereço local podem ser um endereço de host único, o endereço IP do par no nível de protocolo BGP local:

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

Você deve definir "-EnableBGP" como $True ao criar a conexão:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>Próximas etapas
Consulte [Configurando os Gateways de VPN Ativos para Conexões de VNet com VNet e Entre Locais](vpn-gateway-activeactive-rm-powershell.md) para ver as etapas de configuração das conexões ativas entre os locais e conexões VNet com VNet.


