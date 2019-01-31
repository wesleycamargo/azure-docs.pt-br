---
title: Categorizando imagens - Visão por Computador
titleSuffix: Azure Cognitive Services
description: Aprenda mais sobre conceitos relacionados ao recurso de categorização de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f182110d150583ee1c241c23a2e1924d9f3e3bd4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158036"
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
