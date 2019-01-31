---
title: Entidade ordinal predefinida
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de ordinal de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 22b66b36eb3bde4e5953435d3ecf9d872d999589
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218692"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entidade ordinal predefinida para um aplicativo LUIS
Número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first`, `second`, `third`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo ordinal às intenções do aplicativo. A entidade ordinal é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipos de ordinal
A entidade ordinal é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.ordinal**.

```json
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
