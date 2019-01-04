---
title: Entidades de número telefônico predefinidas
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de número de telefone de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3a538886b0137e1069af1fa680374b749a9b3f92
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165395"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Entidade de número telefônico predefinida para um aplicativo LUIS
A entidade `phonenumber` extrai uma variedade de números de telefone, incluindo o código do país. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo ao aplicativo. A entidade `phonenumber` é compatível somente com a cultura `en-us`. 

## <a name="types-of-phonenumber"></a>Tipos de número de telefone
O número de telefone é gerenciado por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Resolução de entidade número de telefone predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.phonenumber**.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md). 