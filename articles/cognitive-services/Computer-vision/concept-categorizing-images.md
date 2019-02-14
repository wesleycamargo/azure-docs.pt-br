---
title: Categorizando imagens - Visão por Computador
titleSuffix: Azure Cognitive Services
description: Aprenda mais sobre conceitos relacionados ao recurso de categorização de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1967ba60088cae2b946cfcfe1467c2de5aebccdf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879944"
---
# <a name="image-categorization-with-computer-vision"></a>Categorização de imagem com Visual Computacional

Além de marcação e descrições, o Computer Vision retorna as categorias baseadas em taxonomia definidas nas versões anteriores. Essas categorias são organizadas como uma taxonomia com hierarquias hereditárias de pai/filho. Todas as categorias estão disponíveis em inglês. Eles podem ser usados sozinhos ou com nossos novos modelos de marcação.

## <a name="the-86-category-concept"></a>O conceito de 86 categorias

Com base em uma lista de 86 conceitos vistos no diagrama a seguir, uma imagem pode ser categorizada de ampla a específica. Para obter a taxonomia completa em formato de texto, confira [Taxonomia de categoria](category-taxonomy.md).

![Listas agrupadas de todas as categorias na categoria taxonomia](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exemplos de categorização de imagens

A resposta JSON a seguir ilustra o que a Computer Vision retorna ao categorizar a imagem de exemplo com base em seus recursos visuais.

![Uma mulher no telhado de um edifício residencial](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

A tabela a seguir ilustra um conjunto de imagens típico e a categoria retornada pelo Computer Vision para cada imagem.

| Imagem | Categoria |
|-------|----------|
| ![Quatro pessoas posando juntas como uma família](./Images/family_photo.png) | people_group |
| ![Um filhote de cão sentado em um gramado](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa em uma rocha na montanha ao pôr do sol](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma montanha de pães em uma mesa](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [marcação de imagens](concept-tagging-images.md) e [descrevendo imagens](concept-describing-images.md).
