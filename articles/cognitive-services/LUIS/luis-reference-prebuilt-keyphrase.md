---
title: Entidade predefinida KeyPhrase
titleSuffix: Azure
description: Este artigo contém informações da entidade pré-compilada KeyPhrase no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: b8bff1ecce7a50f819d00db243eb3e7868c9982e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869019"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida keyPhrase para um aplicativo LUIS
A KeyPhrase extrai uma variedade de frases-chave de um enunciado. Não é necessário adicionar expressões que contenham keyPhrase ao aplicativo. A entidade keyPhrase tem suporte em [muitas culturas](luis-language-support.md#languages-supported) como parte dos recursos de [análise de texto](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Resolução para entidade keyPhrase pré-compilada
O exemplo a seguir mostra a resolução da entidade **builtin.keyPhrase**.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [idade](luis-reference-prebuilt-age.md).
