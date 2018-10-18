---
title: Gerar miniaturas - pesquisa Visual computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à geração de miniaturas de imagens usando a API do Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 9cb82b40d1fbec513b0219f26d1959fbd7f64570
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343955"
---
# <a name="generating-thumbnails"></a>Gerar miniaturas

Uma miniatura é uma representação pequena de uma imagem em tamanho original. Dispositivos variados, como telefones, tablets e computadores, criam uma necessidade de diferentes layouts de experiência do usuário e tamanhos de miniatura. Usando o recorte inteligente, esse recurso de visão computacional ajuda a resolver o problema.

Após o upload de uma imagem, o Computer Vision gera uma miniatura de alta qualidade e analisa os objetos dentro da imagem para identificar a *região de interesse* (ROI). Opcionalmente, ele pode cortar a imagem para atender às necessidades do ROI. A miniatura gerada pode ser apresentada usando uma proporção diferente da proporção da imagem original, para acomodar suas necessidades.

O algoritmo de miniatura funciona da seguinte maneira:

1. Remove os elementos que causam distração da imagem e identifica o objeto principal, a região de interesse.
2. Corta a imagem com base na região de interesse identificada.
3. Altera a taxa de proporção para se ajustar às dimensões da miniatura de destino.

Remove os elementos que causam distração da imagem e identifica o objeto principal, a região de interesse.

![Miniaturas](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Exemplos de geração de miniaturas

A tabela a seguir ilustra as miniaturas típicas geradas pelo Computer Vision para as imagens de exemplo. As miniaturas foram geradas para uma altura e largura de destino especificada de 50 pixels, com recorte inteligente ativado.

| Imagem | Miniatura |
|-------|-----------|
|![Montanha ao ar livre](./Images/mountain_vista.png) | ![Miniatura de montanha ao ar livre](./Images/mountain_vista_thumbnail.png) |
|![Flor com análise da pesquisa visual](./Images/flower.png) | ![Miniatura de analisar Flor de visão](./Images/flower_thumbnail.png) |
|![Mulher no teto](./Images/woman_roof.png) | ![Miniatura de teto Mulher](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos [marcação imagens](concept-tagging-images.md) e [categorizar imagens](concept-categorizing-images.md).