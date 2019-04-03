---
title: Monitorar as métricas de serviços de mídia e os logs de diagnóstico por meio do Azure Monitor | Microsoft Docs
description: Este artigo fornece uma visão geral de como monitorar as métricas de serviços de mídia e os logs de diagnóstico por meio do Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848041"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorar logs de diagnóstico e métricas de serviços de mídia

[O Azure Monitor](../../azure-monitor/overview.md) habilita você a monitorar métricas e logs de diagnóstico que ajudam você a entende como estão o desempenho de seus aplicativos. Todos os dados coletados pelo Azure Monitor se encaixa em um dos dois tipos fundamentais, log e métricas. Você pode monitorar os logs de diagnóstico dos serviços de mídia e criar alertas e notificações para os logs e métricas coletadas. Você pode visualizar e analisar os dados de métricas usando [Metrics explorer](../../azure-monitor/platform/metrics-getting-started.md). Você pode enviar logs para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de eventos](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou usar os serviços de terceiros 3ª.

Para obter uma visão detalhada, consulte [métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md) e [logs de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este tópico discute disponíveis no momento [métricas de serviços de mídia](#media-services-metrics) e [logs de diagnóstico de serviços de mídia](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métricas de serviços de mídia

As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Elas são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples.

Atualmente, os seguintes serviços de mídia [pontos de extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) métricas são emitidas pelo Azure:

|NOME|DESCRIÇÃO|
|---|---|
|Requests|Fornece detalhes sobre o número total de solicitações atendidas pelo ponto de extremidade de streaming.|
|Saída|Número total de bytes de saída. Por exemplo, bytes transmitidos pelo ponto de extremidade de streaming.|
|Latência de ponta a ponta com sucesso| Fornece informações sobre a latência de ponta a ponta de solicitações bem-sucedidas.|

Por exemplo, para obter as métricas de "Saída" com a CLI, você executaria o seguinte `az monitor metrics` comando da CLI:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter informações sobre como criar alertas de métrica, consulte [criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Logs de diagnóstico dos serviços de mídia

No momento, você pode obter os logs de diagnóstico a seguir:

|NOME|DESCRIÇÃO|
|---|---|
|Solicitação de serviço de distribuição de chaves|Logs que mostram as informações de solicitação de serviço de distribuição de chaves. Para obter mais detalhes, consulte [esquemas](media-services-diagnostic-logs-schema.md).|

Para habilitar o armazenamento dos logs de diagnóstico em uma conta de armazenamento, você executaria o seguinte `az monitor diagnostic-settings` comando da CLI: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Por exemplo: 

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Próximas etapas 

[Como coletar e consumir dados de log dos recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
