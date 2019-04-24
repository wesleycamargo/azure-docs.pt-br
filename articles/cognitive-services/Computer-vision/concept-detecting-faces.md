---
title: Detecção facial - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados ao recurso de detecção de face da API de visão do computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 699192aba87bb009d7dbddddcc9579883bb71db9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368096"
---
# <a name="face-detection-with-computer-vision"></a>Detecção facial com Pesquisa Visual Computacional

A Pesquisa Visual Computacional pode detectar rostos humanos em uma imagem e gerar a idade, o sexo e um retângulo para cada rosto detectado. 

> [!NOTE]
> Esse recurso também é oferecido pelo serviço Azure [Face](/azure/cognitive-services/face/). Consulte essa alternativa para obter uma análise de rosto mais detalhada, incluindo identificação de face e detecção de pose. 

## <a name="face-detection-examples"></a>Exemplos de detecção facial

O exemplo a seguir demonstra a resposta JSON retornada pela Pesquisa Visual Computacional de uma imagem contendo um único rosto humano.

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

O próximo exemplo demonstra a resposta JSON retornada de uma imagem contendo vários rostos humanos.

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

## <a name="next-steps"></a>Próximos passos

Consulte a documentação de referência de [Analisar imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para saber mais sobre como usar o recurso de detecção facial.
