---
title: Referência de idade de entidades predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida e idade em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 851e658c68c845c900aee9a4c4c4780f72e83725
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033015"
---
# <a name="age-entity"></a>Entidade de idade
A entidade de idade predefinida captura o valor de idade tanto numericamente quanto em termos de dias, semanas, meses e anos. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados contendo email às intenções do aplicativo. A entidade de idade é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Tipos de idade
A idade é gerenciada por meio do repositório GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Resolução para entidade de idade predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.age**.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [moeda](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [dimensão](luis-reference-prebuilt-dimension.md). 