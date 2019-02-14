---
title: Entidades de dimensão predefinidas
titleSuffix: Azure
description: Este artigo contém informações de entidade predefinida de dimensão em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 88a6c25d5b9cc2697482c400c9672d41102aa35b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879776"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entidade de dimensão predefinida para um aplicativo LUIS
A entidade de dimensão predefinida detecta vários tipos de dimensões, independentemente da cultura do aplicativo do LUIS. Como essa entidade já está treinada, não é necessário adicionar expressões contendo dimensões às intenções do aplicativo. A entidade de dimensão tem suporte em [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Resolução para entidade de dimensão
O exemplo a seguir mostra a resolução da entidade **builtin.dimension**.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) e [ordinal](luis-reference-prebuilt-ordinal.md). 
