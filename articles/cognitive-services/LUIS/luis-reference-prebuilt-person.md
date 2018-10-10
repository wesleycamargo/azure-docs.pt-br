---
title: Entidade PersonName pré-criada – referência do LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade personName pré-criada no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950566"
---
# <a name="personname-entity"></a>Entidade PersonName
A entidade personName pré-criada detecta nomes de pessoas. Como essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo personName nas intenções do aplicativo. A entidade personName tem suporte nas [culturas](luis-reference-prebuilt-entities.md) em inglês e em chinês.

## <a name="resolution-for-personname-entity"></a>Resolução da entidade personName
O exemplo a seguir mostra a resolução da entidade **builtin.personName**.

```JSON
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) e [ordinal](luis-reference-prebuilt-ordinal.md). 