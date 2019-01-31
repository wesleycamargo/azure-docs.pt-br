---
title: Referência de entidades de email predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações de entidade predefinida de email de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 080c4d08348265ea891dd02ff3aa2fe8ba2ad2f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221480"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entidade de email predefinida para um aplicativo LUIS
Extração de email inclui todo o endereço de email inteira de um enunciado. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo email às intenções do aplicativo. Há suporte para a entidade de email apenas na cultura `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Resolução de email predefinido
O exemplo a seguir mostra a resolução da entidade **builtin.email**.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) e [percentual](luis-reference-prebuilt-percentage.md). 
