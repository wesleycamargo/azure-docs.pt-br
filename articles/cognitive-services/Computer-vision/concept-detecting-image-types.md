---
title: Detectar tipos de imagem - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de tipos de imagem usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: ecdbdd60af41ee14070f2f45dfe50875e38ac3b3
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341985"
---
# <a name="detecting-image-types"></a>Identificar tipos de imagens

A Pesquisa Visual Computacional pode analisar o tipo de conteúdo das imagens, indicando se uma imagem é clip-art, classificando a probabilidade em uma escala ou um desenho de linha.

## <a name="detecting-clip-art"></a>Detectar clip-art

A Pesquisa Visual Computacional analisa uma imagem e classifica a probabilidade da imagem ser clip-art em uma escala de 0 a 3, conforme descrito na tabela a seguir.

| Valor | Significado |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Ambíguo |
| 2 | Normal-clip-art |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Exemplos de detecção de clip-art

As respostas JSON a seguir ilustram o que a Pesquisa Visual Computacional retorna ao avaliar a probabilidade das imagens de exemplo serem clip-art.

![Clip-art de queijo com análise da pesquisa visual](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Quintal da casa com análise da pesquisa visual](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Detectar desenhos de linha

A Pesquisa Visual Computacional analisa uma imagem e retorna um valor booleano indicando se a imagem é um desenho de linha.

### <a name="line-drawing-detection-examples"></a>Exemplos de detecção de desenho de linha

As respostas JSON a seguir ilustram o que a Pesquisa Visual Computacional retorna ao indicar se as imagens de exemplo são desenhos de linha.

![Desenho de leão com análise da pesquisa visual](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Flor com análise da pesquisa visual](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [marcando imagens](concept-tagging-images.md) e [categorizando imagens](concept-categorizing-images.md).