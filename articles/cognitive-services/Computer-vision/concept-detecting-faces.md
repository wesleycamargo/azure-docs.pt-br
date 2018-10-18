---
title: Detecção facial - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção facial usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: bf358d1e8f60f989ced8db966bbf0a5179fab25b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342306"
---
# <a name="detecting-faces"></a>Detendo faces

A Pesquisa Visual Computacional detecta rostos humanos em uma imagem e gera a idade, o sexo e enquadra cada rosto detectado. A Pesquisa Visual Computacional oferece um subconjunto da funcionalidade que pode ser encontrado na [Detecção Facial](/azure/cognitive-services/face/), e você pode usar o serviço Detecção Facial para uma análise mais detalhada, como identificação facial e detecção de pose.  

## <a name="face-detection-examples"></a>Exemplos de detecção facial

O primeiro exemplo demonstra a resposta JSON retornada pela Pesquisa Visual Computacional de uma imagem contendo um único rosto humano.

![Face de mulher no teto com análise da pesquisa visual](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

O segundo exemplo demonstra a resposta JSON retornada de uma imagem contendo vários rostos humanos.

![Detecção facial de foto de família com análise da pesquisa visual](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [ detectar conteúdo específico de domínio](concept-detecting-domain-content.md).