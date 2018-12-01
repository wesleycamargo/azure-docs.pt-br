---
title: Referência para entidades pré-compiladas KeyPhrase no LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações da entidade pré-compilada KeyPhrase no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a543e60c6e77ed9fdb825ad6cb2a936119677671
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334577"
---
# <a name="keyphrase-entity"></a>Entidade de KeyPhrase
A KeyPhrase extrai uma variedade de frases-chave de um enunciado. Não é necessário adicionar expressões que contenham keyPhrase ao aplicativo. A entidade keyPhrase tem suporte em [muitas culturas](luis-language-support.md#languages-supported) como parte dos recursos de [análise de texto](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Resolução para entidade keyPhrase pré-compilada
O exemplo a seguir mostra a resolução da entidade **builtin.keyPhrase**.

```JSON
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
