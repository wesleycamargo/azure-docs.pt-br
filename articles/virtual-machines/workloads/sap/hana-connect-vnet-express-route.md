---
title: Configuração de conectividade da rede virtual para o SAP HANA no Azure (grandes instâncias) | Microsoft Docs
description: Configuração de conectividade da rede virtual para usar o SAP HANA no Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a64a60603cd9898386a975313afc676e3b253326
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353590"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Conecte uma rede virtual a instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, você pode conectar essa rede ao SAP HANA em instâncias grandes do Azure. Crie um gateway do Azure ExpressRoute na rede virtual. Esse gateway permite vincular a rede virtual ao circuito da Rota Expressa que se conecta ao inquilino do cliente no carimbo de instância grande.

> [!NOTE] 
> Essa etapa pode levar até 30 minutos para ser concluída. O novo gateway é criado na assinatura do Azure designada e, em seguida, conectado à rede virtual do Azure especificada.

Se um gateway já existir, verifique se é um gateway do ExpressRoute ou não. Caso contrário, exclua o gateway e recrie-o como um gateway do ExpressRoute. Se um gateway da Rota Expressa já estiver estabelecido, consulte a seção a seguir deste artigo, "Vincular redes virtuais". 

- Use o [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à sua rede virtual.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você usar o PowerShell, primeiro Baixe e use a versão mais recente [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/). Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por _$_ são variáveis definidas pelo usuário que devem ser atualizadas com suas informações específicas.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. Suas opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway suportadas para o SAP HANA no Azure (grandes instâncias).

> [!IMPORTANT]
> Para instâncias grandes do HANA do SKU de classe Tipo II, você deve usar o SKU do Gateway UltraPerformance.

## <a name="link-virtual-networks"></a>Vincular redes virtuais

A rede virtual do Azure agora tem um gateway da Rota Expressa. Use as informações de autorização fornecidas pela Microsoft para conectar o gateway ExpressRoute ao circuito ExpressRoute do SAP HANA no Azure (instâncias grandes). Você pode se conectar usando o portal do Azure ou o PowerShell. O portal é recomendado, mas se você quiser usar o PowerShell, as instruções são as seguintes. 

Execute os seguintes comandos para cada gateway de rede virtual usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir são provenientes das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Se você quiser adicionar outra rede virtual do Azure, precisará obter outra AuthID para o circuito da Rota Expressa que conecte instâncias grandes do HANA ao Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Para conectar o gateway a mais de um circuito do ExpressRoute associado à sua assinatura, talvez seja necessário executar essa etapa mais de uma vez. Por exemplo, você provavelmente conectará o mesmo gateway de rede virtual ao circuito da Rota Expressa que conecta a rede virtual à sua rede local.

## <a name="next-steps"></a>Próximas etapas

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)