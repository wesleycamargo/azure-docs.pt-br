---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418221"
---
Para as SKUs atuais (VpnGw1, VpnGw2 e VPNGW3) para o qual você deseja redimensionar sua SKU de gateway para atualizar para uma mais avançada, você pode usar o `Resize-AzVirtualNetworkGateway` cmdlet do PowerShell. Você também pode fazer o downgrade do tamanho do SKU de gateway usando esse cmdlet. Se você estiver usando a SKU de gateway básica [use estas instruções](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) para redimensionar o gateway.

O exemplo de PowerShell a seguir mostra um SKU de gateway sendo redimensionado para VpnGw2.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Você também pode redimensionar um gateway no portal do Azure, vá para o **Configuração** página do seu gateway de rede virtual e selecione uma SKU diferente na lista suspensa.