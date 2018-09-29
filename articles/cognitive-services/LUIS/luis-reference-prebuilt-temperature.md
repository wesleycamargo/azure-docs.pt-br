---
title: Referência entidades de temperatura predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de temperatura de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 39d33a3132a400ce0f1853062968cfdaf81c693f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041465"
---
# <a name="temperature-entity"></a>Entidade de temperatura
Temperatura extrai uma variedade de tipos de temperatura. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo temperatura ao aplicativo. A entidade de temperatura é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Tipos de temperatura
A temperatura é gerenciada usando o repositório GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Resolução de entidade de temperatura predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.temperature**.

```JSON
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [idade](luis-reference-prebuilt-age.md). 