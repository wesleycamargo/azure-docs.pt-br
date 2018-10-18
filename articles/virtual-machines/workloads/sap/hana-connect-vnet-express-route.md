---
title: Configuração de conectividade da rede virtual para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Configuração de conectividade da rede virtual para usar o SAP HANA no Azure (Instâncias Grandes).
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
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167569"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Conectar uma Rede Virtual a ExpressRoute de Instância Grande do HANA

Como você definiu todos os intervalos de endereço IP e agora tem os dados de volta da Microsoft, você pode começar a conectar a rede virtual que você criou antes do HANA grandes instâncias. Depois de criar a rede virtual do Azure, um gateway de ExpressRoute deve ser criado na rede virtual para vincular a rede virtual ao circuito de ExpressRoute que se conecta ao locatário do cliente no carimbo de instância grande.

> [!NOTE] 
> Esta etapa pode levar até 30 minutos para ser concluída, pois o novo gateway é criado na assinatura do Azure designada e depois conectado à rede virtual do Azure especificada.

Se já existir um gateway, verifique se ele é um gateway de ExpressRoute ou não. Caso contrário, o gateway deve ser excluído e recriado como um gateway de ExpressRoute. Se um gateway de ExpressRoute já estiver estabelecido, uma vez que a rede virtual do Azure já está conectada ao circuito de ExpressRoute para conectividade local, vá para a seção Vinculação de redes virtuais abaixo.

- Use o (novo) [portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à VNet.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você escolher o PowerShell, primeiro baixe e use a versão mais recente do [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/) para garantir uma experiência ideal. Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo usuário que precisam ser atualizadas com as informações específicas.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

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

Neste exemplo, a SKU de gateway HighPerformance foi usada. As opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway que são suportadas para SAP HANA no Azure (Instâncias Grandes).

> [!IMPORTANT]
> Para as Instâncias Grandes de HANA de SKU de classe to Tipo II, o uso da SKU do Gateway UltraPerfomance é obrigatório.

**Como vincular VNets**

Agora que a rede virtual do Azure tem um gateway de ExpressRoute, você usa as informações de autorização fornecidas pela Microsoft para conectar o gateway de ExpressRoute para o SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) criado para essa conectividade. Esse etapa pode ser realizada usando o portal do Azure ou o PowerShell. O portal é recomendável, porém, as instruções do PowerShell são descritas a seguir. 

- Execute os comandos a seguir para cada gateway de VNet usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir foram obtidas das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Isso significa que, se você desejar adicionar outra VNet do Azure, precisará obter outra AuthID para o circuito do ExpressRoute que conecta as Instâncias Grandes do HANA no Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
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

Se desejar conectar o gateway a vários circuitos do ExpressRoute associados à sua assinatura, talvez você precise realizar essa etapa mais de uma vez. Por exemplo, provavelmente, você conectará o mesmo Gateway de VNet ao circuito do ExpressRoute que conecta a VNet à rede local.

**Próximas etapas**

- Veja [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md).