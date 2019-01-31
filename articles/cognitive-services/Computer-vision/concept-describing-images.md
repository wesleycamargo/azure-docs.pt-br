---
title: Descrevendo imagens – pesquisa Visual computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao recurso de descrição de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 933c4e4a6731a492a5aa461de7c782af422fb6fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162694"
---
# <a name="describe-images-with-human-readable-language"></a>Descrever imagens com linguagem legível por humanos

Algoritmos do pesquisa Visual computacional analisam o conteúdo em uma imagem. Essa análise constitui a base para a 'description' exibida como um idioma legível por humanos em sentenças completas. A descrição resume o que é encontrado na imagem. Os algoritmos da Computer Vision geram várias descrições baseadas nos recursos visuais identificados na imagem. Cada descrição é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa.

## <a name="image-description-example"></a>Exemplo de descrição de imagem

A resposta JSON a seguir ilustra o que o Computer Vision retorna ao descrever a imagem de exemplo com base em seus recursos visuais.

![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [marcando imagens](concept-tagging-images.md) e [categorizando imagens](concept-categorizing-images.md).
