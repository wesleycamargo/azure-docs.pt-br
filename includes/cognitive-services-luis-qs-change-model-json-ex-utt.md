---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: a1b0afce31d7202c38b049addf546350ff347719
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044116"
---
O arquivo de enunciados de exemplo, **utterances.json**, segue um formato específico. 

O campo `text` contém o texto do enunciado de exemplo. O campo `intentName` precisa corresponder ao nome de uma intenção existente no aplicativo do LUIS. O campo `entityLabels` é obrigatório. Se você não quiser rotular as entidades, forneça uma matriz vazia.

Se a matriz entityLabels não estiver vazia, `startCharIndex` e `endCharIndex` precisarão marcar a entidade referenciada no campo `entityName`. O índice é baseado em zero, o que significa que o 6 no exemplo superior se refere ao "S" de Seattle e não ao espaço antes da letra maiúscula S. Se você iniciar ou terminar o rótulo com um espaço no texto, a chamada à API para adicionar os enunciados falhará.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```