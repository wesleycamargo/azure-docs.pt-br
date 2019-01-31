---
title: Entidades de URL predefinidas
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida URL de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: f9e331abd31ef37e9214214118748ebda3eb9470
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225781"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entidade de URL predefinida para um aplicativo LUIS
A entidade de URL extrai URLs com nomes de domínio ou endereços IP. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo URLs ao aplicativo. A entidade de URL é compatível somente com a cultura `en-us`. 

## <a name="types-of-urls"></a>Tipos de URLs
A URL é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução para a entidade URL predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.url**.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
