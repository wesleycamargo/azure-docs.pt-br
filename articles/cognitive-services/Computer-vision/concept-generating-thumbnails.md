---
title: Gerar miniaturas - pesquisa Visual computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à geração de miniaturas de imagens usando a API do Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8bbc86f5c6fe0f30968a1ba5bd5fa28160ef6963
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372803"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Gerar miniaturas de corte inteligente com Pesquisa Visual Computacional

Uma miniatura é uma representação em tamanho reduzido de uma imagem. As miniaturas são usadas para representar as imagens e outros dados de uma maneira mais econômica e com um layout mais fácil. A API da Pesquisa Visual Computacional usa o corte inteligente, junto com o redimensionamento de imagem, para criar miniaturas intuitivas para uma determinada imagem.

O algoritmo de geração de miniatura da Pesquisa Visual Computacional funciona da seguinte maneira:

1. remova os elementos de distração da imagem e identifique as _área de interesse_&mdash;a área da imagem na qual o objeto principal é exibido.
1. Corte a imagem com base na _área de interesse_ identificada.
1. Altere a taxa de proporção para se ajustar às dimensões da miniatura de destino.

## <a name="area-of-interest"></a>Área de interesse

Ao carregar uma imagem, a API da Pesquisa Visual Computacional faz a análise para determinar a *área de interesse*. Ela pode usar essa região para determinar como cortar a imagem. A operação de corte, no entanto, sempre corresponderá à taxa de proporção desejada se uma for especificada.

Você também pode obter as coordenadas de caixa delimitadora bruta dessa mesma *área de interesse* chamando a API **areaOfInterest**. Em seguida, você pode usar essas informações para modificar a imagem original, se quiser.

## <a name="examples"></a>Exemplos

Remove os elementos que causam distração da imagem e identifica o objeto principal, a região de interesse.

![Uma imagem de mountain ao lado de várias configurações de corte](./Images/thumbnail-demo.png)

A tabela a seguir ilustra as miniaturas típicas geradas pelo Computer Vision para as imagens de exemplo. As miniaturas foram geradas para uma altura e largura de destino especificada de 50 pixels, com recorte inteligente ativado.

| Image | Miniatura |
|-------|-----------|
|![Com visibilidade em exteriores Mountain em pôr do sol, com a silhueta da pessoa](./Images/mountain_vista.png) | ![Miniatura de Mountain com visibilidade em exteriores pôr do sol, com a silhueta da pessoa](./Images/mountain_vista_thumbnail.png) |
|![Uma flor branca com um fundo verde](./Images/flower.png) | ![Miniatura de analisar Flor de visão](./Images/flower_thumbnail.png) |
|![Uma mulher no telhado de um edifício residencial](./Images/woman_roof.png) | ![miniatura de uma mulher no telhado de um edifício de apartment](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Próximas etapas

Aprenda sobre [marcação de imagens](concept-tagging-images.md) e [categorização de imagens](concept-categorizing-images.md).
