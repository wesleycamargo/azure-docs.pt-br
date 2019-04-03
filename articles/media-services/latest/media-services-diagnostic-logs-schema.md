---
title: Logs de serviços de mídia do Azure diagnóstico esquemas – Azure
description: Este artigo mostra os esquemas de logs de diagnóstico dos serviços de mídia do Azure.
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850639"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas de logs de diagnóstico

[O Azure Monitor](../../azure-monitor/overview.md) habilita você a monitorar métricas e logs de diagnóstico que ajudam você a entende como estão o desempenho de seus aplicativos. Você pode monitorar os logs de diagnóstico dos serviços de mídia e criar alertas e notificações para os logs e métricas coletadas. Você pode enviar logs para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de eventos](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou usar os serviços de terceiros 3ª.

Para obter informações detalhadas, consulte [métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md) e [logs de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este artigo descreve os esquemas de logs de diagnóstico dos serviços de mídia.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de logs de diagnóstico de nível superior

Para obter uma descrição detalhada do esquema de logs de diagnóstico de nível superior, consulte [com suporte a serviços, esquemas e categorias para os Logs de diagnóstico do Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Esquema do log de distribuição de chaves

### <a name="properties"></a>propriedades

Essas propriedades são específicas para o esquema de log de distribuição de chaves.

|NOME|DESCRIÇÃO|
|---|---|
|keyId|A ID da chave solicitada.|
|keyType|Pode ser um dos seguintes valores: "Limpar" (sem criptografia), "FairPlay", "PlayReady" ou "Widevine".|
|policyName|O nome do Azure Resource Manager da política.|
|tokenType|O tipo de token.|
|statusMessage|A mensagem de status.|

### <a name="examples"></a>Exemplos

Propriedades do esquema de solicitações de distribuição de chaves.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Próximas etapas

[Monitorar logs de diagnóstico e métricas de serviços de mídia](media-services-metrics-diagnostic-logs.md)
