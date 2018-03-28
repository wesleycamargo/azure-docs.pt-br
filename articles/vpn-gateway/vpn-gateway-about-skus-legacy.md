---
title: SKUs de gateway de VPN de rede virtual herdadas do Azure | Microsoft Docs
description: Como trabalhar com as SKUs do gateway de rede virtual antigas; Basic, Standard e de Alto desempenho.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com SKUs de gateway de rede virtual (SKUs herdadas)

Este artigo contém informações sobre as SKUs de gateway de rede virtual herdadas (antigas). As SKUs herdadas ainda funcionam em ambos os modelos de implantação de gateways de VPN que já foram criados. Os gateways de VPN clássicos continuam a usar as SKUs herdadas, para os gateways existentes e para os novos gateways. Ao criar novos gateways de VPN do Resource Manager, use as novas SKUs de gateway. Para saber mais sobre os novo SKUs, veja [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKUs do Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Taxa de transferência agregada estimada por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurações com suporte pelo tipo de SKU e de VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionar um gateway

Você pode redimensionar sua gateway para uma SKU de gateway dentro da mesma família de SKUs. Por exemplo, se você tiver uma SKU Standard, poderá redimensionar para uma SKU HighPerformance. No entanto, você não pode redimensionar seu gateway de VPN entre as SKUs antigas e as novas famílias de SKU. Por exemplo, você não pode ir de uma SKU padrão para uma SKU VpnGw2, ou de uma SKU Básica para VpnGw1.

Para redimensionar um gateway para o modelo de implantação clássico, use o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Para redimensionar um gateway para o modelo de implantação do Gerenciador de Recursos usando o PowerShell, use o seguinte comando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Você também pode redimensionar um gateway no portal do Azure.

## <a name="change"></a>Alterar para as novas SKUs de gateway

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as novas SKUs do Gateway, consulte [SKUs de Gateway](vpn-gateway-about-vpngateways.md#gwsku).

Para saber mais sobre definições de configuração, veja [Sobre definições de configuração do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).