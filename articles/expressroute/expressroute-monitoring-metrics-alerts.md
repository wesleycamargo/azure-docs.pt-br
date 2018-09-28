---
title: Monitoramento, métricas e alertas do Azure ExpressRoute | Microsoft Docs
description: Esta página fornece informações sobre o monitoramento do ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 73bacebe6edc5b1d1273a10a0db7397766c45b0c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971630"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoramento, alertas e métricas do ExpressRoute

 Este artigo ajuda você a entender o monitoramento, as métricas e os alertas do ExpressRoute. O Azure Monitor é uma opção completa para todas as métricas, alertas e logs de diagnóstico em todo o Azure.

## <a name="circuit-metrics"></a>Métricas de circuito

Para navegar até **Métricas**, clique na página do ExpressRoute para o circuito que você deseja monitorar. Em **Monitoramento**, você pode exibir as **Métricas**.

![métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Métricas por emparelhamento

Você pode exibir métricas para emparelhamento privado, público e da Microsoft em bits por segundo.

![métricas por emparelhamento](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Conexões de gateway do ExpressRoute em bits por segundos

![conexões de gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para conexões de gateway do ExpressRoute

1. Para configurar alertas, navegue até **Azure Monitor** e clique em **Alertas**.

  ![alertas](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique em **+ Selecionar destino** e selecione o recurso de conexão de gateway do ExpressRoute.

  ![destino]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

  ![grupo de ações](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)


4. Defina e adicione o grupo de ações.

  ![adicionar grupo de ações](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas com base em cada emparelhamento

 ![o quê](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurar alertas para os logs de atividade em circuitos

Nos **Critérios de Alerta**, você pode selecionar **Log de Atividades** para o tipo de sinal e selecione o sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)

## <a name="next-steps"></a>Próximas etapas
* Configurar sua conexão do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
