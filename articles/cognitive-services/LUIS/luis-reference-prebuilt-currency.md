---
title: Entidade de moeda predefinida
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de moeda de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 76531e3ebba5519d9ddab7733abb2890003cbcd5
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339905"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entidade de moeda predefinida para um aplicativo LUIS
A entidade de moeda predefinida detecta moeda em muitas denominações e países, independentemente da cultura do aplicativo de LUIS. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo moeda às intenções do aplicativo. A entidade de moeda é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Tipos de moeda
A moeda é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Resolução de entidades de moeda
O exemplo a seguir mostra a resolução da entidade **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensão](luis-reference-prebuilt-dimension.md) e [email](luis-reference-prebuilt-email.md). 
