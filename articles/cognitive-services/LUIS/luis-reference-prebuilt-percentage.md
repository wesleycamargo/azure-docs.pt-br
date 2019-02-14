---
title: Entidade de percentual predefinida
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de percentual de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 0e06857fe45b81bcb6696c29b6a3e07a6587a95b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874661"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Entidade de percentual predefinida para um aplicativo LUIS
Números de percentual podem aparecer como frações, `3 1/2` ou como percentual, `2%`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo percentual às intenções do aplicativo. A entidade de percentual é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Tipos de percentual
O percentual é gerenciado usando o repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Resolução para entidade de percentual predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.percentage**.

```json
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
