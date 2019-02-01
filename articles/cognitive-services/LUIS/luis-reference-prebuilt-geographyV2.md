---
title: Entidade Geography V2 predefinida
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações da entidade predefinida geographyV2 no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e794ba86500e1c149deeafeaba508b3429ac590c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221446"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>Entidade GeographyV2 predefinida para um aplicativo LUIS
A entidade geographyV2 predefinida detecta locais. Como essa entidade já está treinada, não é necessário adicionar enunciados contendo GeographyV2 às intenções do aplicativo. Há suporte para a entidade GeographyV2 na [cultura](luis-reference-prebuilt-entities.md) de inglês.

## <a name="subtypes"></a>Subtipos
As localizações geográficas têm subtipos:

|Subtype|Finalidade|
|--|--|
|`poi`|ponto de interesse|
|`city`|nome da cidade|
|`countryRegion`|nome de país ou região|
|`continent`|nome do continente|
|`state`|nome do Estado ou província|


## <a name="resolution-for-geographyv2-entity"></a>Resolução para a entidade GeographyV2
O exemplo a seguir mostra a resolução da entidade **builtin.geographyV2**.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) e [ordinal](luis-reference-prebuilt-ordinal.md). 
