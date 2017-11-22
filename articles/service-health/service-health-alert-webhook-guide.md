---
title: "Configurar notificações de integridade para sistemas de gerenciamento de problemas existentes usando um webhook | Microsoft Docs"
description: "Obtenha notificações personalizadas sobre eventos de integridade do serviço para seu sistema de gerenciamento de problemas existente."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configurar notificações de integridade para sistemas de gerenciamento de problemas existentes usando um webhook

Este artigo mostra como configurar os alertas de Integridade do Serviço para enviar dados por meio de Webhooks para seu sistema de notificação existente.

Atualmente, você pode configurar Alertas de Integridade do Serviço para que, quando um Incidente de Serviço do Azure afetá-lo, você seja notificado por email ou mensagem de texto.
No entanto, talvez você já tenha sistema de notificação externa existente que deseja usar.
Este documento mostra as partes mais importantes do conteúdo do webhook e como você pode criar alertas personalizados para ser notificado quando problemas de serviço o afetarem.

Caso deseje usar uma integração pré-configurada, saiba como:
* [Configurar alertas com o ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurar alertas com o PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurar alertas com o OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configurar uma notificação personalizada usando o conteúdo do webhook de integridade do serviço
Caso deseje configurar sua própria integração de webhook personalizada, você precisa analisar o conteúdo do JSON que é enviado durante as notificações de Integridade do Serviço.

Acesse [aqui para ver um exemplo](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) da aparência do conteúdo do webhook `Service Health`.

Detecte se esse é um alerta de integridade do serviço examinando `context.eventSource == "ServiceHealth"`. Nesse local, as propriedades que são mais relevantes para ingestão são:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Criando um link direto à integridade do serviço do Azure para um incidente
Crie um link direto ao incidente personalizado de Integridade do Serviço do Azure na área de trabalho ou no dispositivo móvel gerando uma URL especializada. Use a `trackingId`, bem como o primeiro e os últimos três dígitos de sua `subscriptionId`, para formar:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Por exemplo, se sua `subscriptionId` for `bba14129-e895-429b-8809-278e836ecdb3` e sua `trackingId` for `0DET-URB`, a URL personalizada de Integridade do Serviço do Azure será:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Usando o nível para detectar a gravidade do problema
Da gravidade mais baixa até a mais alta, a propriedade `level` no conteúdo pode ser `Informational`, `Warning`, `Error` e `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analisando os serviços afetados para entender o escopo completo do incidente
Os Alertas de Integridade do Serviço podem informá-lo sobre problemas em várias Regiões e serviços. Para obter os detalhes completos, você precisa analisar o valor de `impactedServices`.
O conteúdo é uma cadeia de caracteres [JSON com escape](http://json.org/); quando sem escape, contém outro objeto JSON que pode ser analisado normalmente.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Torna-se:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Isso mostra que há problemas com “Alertas e Métricas” no Leste e Sudeste da Austrália, bem como problemas com o “Serviço de Aplicativo” no Sudeste da Austrália.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de Integridade do Serviço que você deseja enviar. Encontre um conteúdo de webhook de Integridade do Serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Você deverá receber uma resposta `2XX - Successful`.

4. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Examine o [esquema do webhook de alertas de log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).