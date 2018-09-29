---
title: Referência de entidades de ordinal predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de ordinal de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: cdbe6a04e579c9290c61b36102a66f6a2d8c317d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041873"
---
# <a name="ordinal-entity"></a>Entidade ordinal
Número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first`, `second`, `third`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo ordinal às intenções do aplicativo. A entidade ordinal é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipos de ordinal
O ordinal é gerenciado por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.ordinal**.

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número de telefone](luis-reference-prebuilt-phonenumber.md) e [temperatura](luis-reference-prebuilt-temperature.md). 