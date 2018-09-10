---
title: Referência de dimensão de entidades predefinidas do LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações de entidade predefinida de dimensão em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 3c923e7791c58255690100b04700577eb5c3f5dd
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237257"
---
# <a name="dimension-entity"></a>Entidade de dimensão
A entidade de dimensão predefinida detecta vários tipos de dimensões, independentemente da cultura do aplicativo do LUIS. Como essa entidade já está treinada, não é necessário adicionar expressões contendo dimensões às intenções do aplicativo. A entidade de dimensão tem suporte em [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerenciada a partir do repositório Github [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Resolução para entidade de dimensão
O exemplo a seguir mostra a resolução da entidade **builtin.dimension**.

```JSON
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