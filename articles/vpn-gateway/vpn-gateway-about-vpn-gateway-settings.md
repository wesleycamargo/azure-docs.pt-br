---
title: "Configurações de Gateway de VPN para conexões do Azure entre locais | Microsoft Docs"
description: "Saiba mais sobre as configurações do Gateway de VPN para gateways de rede virtual do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: b902d2e79633959a6f76ddd45b1193177b0e8465
ms.openlocfilehash: 1ac5a78c8d9419e4c641bf66f8dac7aa8cbcd179
ms.contentlocale: pt-br
ms.lasthandoff: 02/14/2017


---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre definições de configuração do Gateway de VPN
Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado entre sua rede virtual e seu local em uma conexão pública. Você também pode usar o Gateway de VPN para enviar o tráfego entre redes virtuais no backbone do Azure.

Uma conexão de Gateway de VPN tem base na configuração de vários recursos, cada um deles contendo definições configuráveis. As seções neste artigo abordam os recursos e configurações relacionados a um gateway de VPN para uma rede virtual criada no modelo de implantação do Resource Manager. Você pode encontrar descrições e diagramas da topologia para cada solução de conexão no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) .  

## <a name="gwtype"></a>Tipos de gateway
Cada rede virtual pode ter apenas um gateway de rede virtual de cada tipo. Quando estiver criando um gateway de rede virtual, você deve garantir que o tipo de gateway seja o correto para sua configuração.

Os valores disponíveis para o -GatewayType são: 

* Vpn
* Rota Expressa

Um gateway de VPN exige o `-GatewayType` *Vpn*.  

Exemplo:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
    
## <a name="gwsku"></a>SKUs do Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-legacy-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configurando SKU de gateway
####<a name="specifying-the-gateway-sku-in-the-azure-portal"></a>Especificação do SKU do gateway no Portal do Azure

Se você usa o portal do Azure para criar um gateway de rede virtual do Resource Manager, é possível selecionar o SKU do gateway usando o menu suspenso. As opções apresentadas correspondem ao tipo de Gateway e ao tipo de VPN que você selecionar.

Por exemplo, se selecionar o tipo de gateway 'VPN' e o tipo de VPN 'Baseado em políticas', você verá apenas o SKU 'Basic' porque é o único disponível para VPNs PolicyBased. Se selecionar 'Baseado em rotas', você pode selecionar entre os SKUs Basic, Standard e HighPerformance. 

####<a name="specifying-the-gateway-sku-using-powershell"></a>Especificando o SKU de gateway usando o PowerShell

O exemplo do PowerShell a seguir especifica o `-GatewaySku` como *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

####<a name="changing-a-gateway-sku"></a>Alterando um SKU de gateway

Se quiser atualizar para um SKU de gateway mais avançado (de Basic/Standard para HighPerformance), você pode usar o cmdlet do PowerShell `Resize-AzureRmVirtualNetworkGateway`. Você também pode fazer o downgrade do tamanho do SKU de gateway usando esse cmdlet.

O exemplo de PowerShell a seguir mostra um SKU de gateway sendo redimensionado para HighPerformance.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Taxa de transferência agregada estimada por SKU de gateway e tipo
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="connectiontype"></a>Tipos de conexão
No modelo de implantação do Resource Manager, cada configuração exige um tipo específico de conexão de gateway de rede virtual. Os valores disponíveis do PowerShell do Gerenciador de Recursos para o `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* Rota Expressa
* VPNClient

No exemplo do PowerShell a seguir, criamos uma conexão S2S que exige o tipo de conexão *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos de VPN
Quando você cria o gateway de rede virtual para uma configuração de gateway de VPN, é preciso especificar um tipo de VPN. O tipo de VPN que você escolhe depende da topologia de conexão que quer criar. Por exemplo, uma conexão P2S requer um tipo de VPN RouteBased. Um tipo de VPN também pode depender do hardware que você usará. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN recebem suporte apenas de um determinado tipo de VPN.

O tipo de VPN que você escolher deve atender a todos os requisitos de conexão da solução que você quer criar. Por exemplo, se você quiser criar uma conexão de gateway de VPN S2S e uma conexão de gateway de VPN P2S para a mesma rede virtual, use o tipo de VPN *RouteBased* , pois P2S requer um tipo de VPN RouteBased. Você também precisa verificar se o seu dispositivo VPN é compatível com uma conexão VPN RouteBased. 

Depois que um gateway de rede virtual é criado, não é possível alterar o tipo de VPN. Você precisa excluir o gateway de rede virtual e criar outro. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo do PowerShell a seguir especifica o `-VpnType` como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

## <a name="requirements"></a>Requisitos do gateway
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sub-rede do gateway
Para configurar um gateway de rede virtual para sua VNet, será necessário criar uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que usam os serviços de gateway de rede virtual. A sub-rede do gateway deve ser nomeada como *GatewaySubnet* para funcionar corretamente. Esse nome permite que o Azure saiba que essa sub-rede deve ser usada para o gateway.

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para o serviço de gateway. Algumas configurações exigem a alocação de mais endereços IP para o serviço de gateway do que outras. Convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar o crescimento futuro e possíveis novas configurações de conexão adicionais. Dessa forma, embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.). Examine os requisitos para a configuração que deseja criar e verifique se a sub-rede de gateway que você tem atende a esses requisitos.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways de rede locais
Ao criar uma configuração de gateway de VPN, o gateway de rede local geralmente representa sua localização no local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local. 

Você dá ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especifica os prefixos de endereço que estão localizados no caminho local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente. Você também especifica os gateways de rede para configurações de rede virtual para rede virtual que usam uma conexão de gateway de VPN.

O seguinte exemplo de PowerShell cria um novo gateway de rede local:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Para uma rede virtual clássica, você pode alterar essas configurações no Portal Clássico na página Redes Locais. Para o Resource Manager, confira [Modificar as configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets do PowerShell e APIs REST
Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar cmdlets do PowerShell e APIs REST para configurações do Gateway de VPN, veja as seguintes páginas:

| **Clássico** | **Gerenciador de Recursos** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Próximas etapas
Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações sobre as configurações de conexão disponíveis. 


