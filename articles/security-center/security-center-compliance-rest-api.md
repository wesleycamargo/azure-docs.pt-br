---
title: Monitoramento de conformidade da Central de Segurança do Azure usando a API REST | Microsoft Docs
description: Examine os resultados de exames de conformidade automatizados usando a API REST da Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819421"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Examinar os resultados de conformidade da Central de Segurança usando as APIs REST do Azure

Neste artigo, você aprenderá a recuperar as informações de conformidade de segurança para uma lista de assinaturas usando as APIs REST do Azure.

## <a name="review-compliance-for-each-subscription"></a>Examinar a conformidade para cada assinatura

O exemplo abaixo obtém informações de avaliação de segurança para uma determinada conformidade e assinatura usando a operação [Obter conformidades](/rest/api/securitycenter/compliances/get).

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

O `{complianceName}` parâmetro é obrigatório e deve conter o nome da conformidade avaliada para `{subscription-id}`. A saída conterá os resultados da avaliação, tais como:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Examinar a conformidade para várias assinaturas

Para obter dados de várias assinaturas, faça a chamada abaixo para primeiro obter uma lista de assinaturas usando a operação [Listar assinaturas](/rest/api/resources/subscriptions/list). Invoque a operação [Obter conformidades](/rest/api/securitycenter/compliances/get) acima para cada uma das IDs de assinatura retornadas.

A chamada à API é:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Retorna uma matriz de valores como a mostrada a seguir. Use os valores de subscriptionId na chamada acima para revisar as informações de conformidade de todas as assinaturas.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






