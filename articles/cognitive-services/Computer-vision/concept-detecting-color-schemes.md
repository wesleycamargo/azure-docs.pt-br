---
title: Detectando esquemas de cores - Computer Vision
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção do esquema de cores em imagens usando a API do Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0793f572e043248af409e65cca4fd854f1371900
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880862"
---
# <a name="detect-color-schemes-in-images"></a>Detectar esquemas de cores em imagens

A Visão por Computador extrai cores de uma imagem. As cores são então analisadas em três contextos diferentes: a cor dominante no primeiro plano, a cor dominante no plano de fundo e as cores dominantes na imagem como um todo. Eles são agrupados em 12 cores de destaque dominantes. Essas cores de destaque são preto, azul, marrom, cinza, verde, laranja, rosa, roxo, vermelho, azul-petróleo, branco e amarelo. O Computer Vision analisa as cores extraídas de uma imagem para retornar uma cor de destaque que representa a cor mais vibrante da imagem para os espectadores, por meio de uma combinação de cores dominantes e saturação. Dependendo das cores de uma imagem, as cores preto e branco simples ou cores de destaque podem ser retornadas em códigos de cores hexadecimais. O Computer Vision também retorna um valor booleano que indica se uma imagem é em preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de detecção do esquema de cores

O exemplo a seguir ilustra a resposta JSON retornada pela visão do computador ao detectar o esquema de cores da imagem de exemplo. Nesse caso, a imagem de exemplo não é uma imagem de preta e branca, mas as cores de primeiro plano e plano de fundo dominantes são pretas e as cores dominantes para a imagem como um todo são preto e branco.

![Montanha ao ar livre](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Exemplos de cor predominante

A tabela a seguir descreve as cores predominantes de primeiro plano, plano de fundo e imagem para cada imagem de exemplo, conforme retornado pela Computer Vision.

| Imagem | Cores predominantes |
|-------|-----------------|
|![Uma flor branca com um fundo verde](./Images/flower.png)| Primeiro plano: Preto<br/>Plano de fundo: Branco<br/>Cores: Preto, Branco, Verde|
![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | Primeiro plano: Preto<br/>Plano de fundo: Preto<br/>Cores: Preto |

### <a name="accent-color-examples"></a>Exemplos de cor de ênfase

 A tabela a seguir descreve a cor de destaque, como um valor hexadecimal de cor HTML, para cada imagem de exemplo conforme retornados pela pesquisa Visual computacional.

| Imagem | Cor de destaque |
|-------|--------------|
|![Uma pessoa que está em uma montanha ao pôr do sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um fundo verde](./Images/flower.png) | #C6A205 |
|![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de detecção de preto e branco

A tabela a seguir indica se cada imagem de exemplo é em preto e branco, conforme retornado por Computer Vision.

| Imagem | Preto e branco? |
|-------|----------------|
|![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png) | verdadeiro |
|![Uma casa azul e o jardim da frente](./Images/house_yard.png) | falso |

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos [detectar tipos de imagem](concept-detecting-image-types.md).
