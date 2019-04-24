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
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203254"
---
# <a name="categorize-images-by-subject-matter"></a>Categorizar imagens por assunto

Além das marcas e da descrição, a Pesquisa Visual Computacional retorna as categorias baseada em taxonomia detectadas em uma imagem. Ao contrário das marcas, as categorias são organizadas em uma hierarquia hereditária pai/filho e há menos delas (86, em vez de milhares de marcas). Todos os nomes de categoria são em inglês. A categorização pode ser feita por si só ou junto com o modelo mais recente de marcas.

## <a name="the-86-category-concept"></a>O conceito de 86 categorias

Pesquisa Visual computacional pode categorizar uma imagem em larga escala ou especificamente, usando a lista de 86 categorias no diagrama a seguir. Para obter a taxonomia completa em formato de texto, confira [Taxonomia de categoria](category-taxonomy.md).

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

| Image | Categoria |
|-------|----------|
| ![Quatro pessoas posando juntas como uma família](./Images/family_photo.png) | people_group |
| ![Um filhote de cão sentado em um gramado](./Images/cute_dog.png) | animal_dog |
| ![Uma pessoa em uma rocha na montanha ao pôr do sol](./Images/mountain_vista.png) | outdoor_mountain |
| ![Uma montanha de pães em uma mesa](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Próximos passos

Aprenda conceitos sobre [marcação de imagens](concept-tagging-images.md) e [descrevendo imagens](concept-describing-images.md).
