---
title: Referência entidades de percentual predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de percentual de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 876ee710491213f17c12d3b6ddd0339bb2043657
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334900"
---
# <a name="percentage-entity"></a>Entidade de percentual
Números de percentual podem aparecer como frações, `3 1/2` ou como percentual, `2%`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo percentual às intenções do aplicativo. A entidade de percentual é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Tipos de percentual
O percentual é gerenciado usando o repositório GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução para entidade de percentual predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.percentage**.

```JSON
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md). 