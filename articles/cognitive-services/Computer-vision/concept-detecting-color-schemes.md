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
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6b25da9b2569b0185d41684c45a22a3eb3377511
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313068"
---
# <a name="detect-color-schemes-in-images"></a>Detectar esquemas de cores em imagens

A Pesquisa Visual Computacional analisa as cores em uma imagem para fornecer três atributos diferentes: a cor de primeiro plano predominante, a cor da tela de fundo predominante e o conjunto de cores predominantes da imagem inteira. As cores retornadas pertencem ao conjunto: preto, azul, marrom, cinza, verde, laranja, rosa, roxo, vermelho, azul-petróleo, branco e amarelo. 

A Pesquisa Visual Computacional extrai também uma cor de destaque, que representa a cor mais vibrante na imagem, com base em uma combinação de cores predominantes e saturação. A cor de destaque é retornada como um código hexadecimal de cor HTML. 

A Pesquisa Visual Computacional também retorna um valor booliano que indica se uma imagem é em preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de detecção do esquema de cores

O exemplo a seguir ilustra a resposta JSON retornada pela visão do computador ao detectar o esquema de cores da imagem de exemplo. Nesse caso, a imagem de exemplo não é em preto e branco, mas as cores de primeiro plano e da tela de fundo predominantes são pretas e as cores predominantes da imagem inteira são em preto e branco.

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

A tabela a seguir mostra o primeiro plano, a tela de fundo e as cores da imagem retornados para cada imagem de exemplo.

| Imagem | Cores predominantes |
|-------|-----------------|
|![Uma flor branca com um fundo verde](./Images/flower.png)| Primeiro plano: Preto<br/>Plano de fundo: Branco<br/>Cores: Preto, Branco, Verde|
![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | Primeiro plano: Preto<br/>Plano de fundo: Preto<br/>Cores: Preto |

### <a name="accent-color-examples"></a>Exemplos de cor de ênfase

 A tabela a seguir mostra a cor de ênfase retornada, como um valor hexadecimal de cor HTML, para cada imagem de exemplo.

| Imagem | Cor de destaque |
|-------|--------------|
|![Uma pessoa que está em uma montanha ao pôr do sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um fundo verde](./Images/flower.png) | #C6A205 |
|![Um treinamento em execução por meio de uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de detecção de preto e branco

A tabela a seguir mostra a avaliação de preto e branco da Pesquisa Visual Computacional nas imagens de exemplo.

| Imagem | Preto e branco? |
|-------|----------------|
|![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png) | verdadeiro |
|![Uma casa azul e o jardim da frente](./Images/house_yard.png) | falso |

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos [detectar tipos de imagem](concept-detecting-image-types.md).
