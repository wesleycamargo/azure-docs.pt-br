---
title: Detecção de objetos – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de objetos usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 89323e584b4020613fe3ff8411df50f2ab96f156
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582721"
---
# <a name="object-detection"></a>Detecção de objetos

A detecção de objetos é semelhante à [marcação](concept-tagging-images.md), mas a API retorna as coordenadas da caixa delimitadora (em pixels) para cada objeto encontrado. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação Detect listará esses objetos junto com as coordenadas na imagem. Você pode usar essa funcionalidade para processar as relações entre os objetos em uma imagem. Ela também permite que você saiba quando há várias instâncias da mesma tag em uma imagem.

A API de Detecção se aplica a tags com base em objetos ou seres vivos identificados na imagem. Observe que, neste ponto, não há uma relação formal entre a taxonomia usada para marcação e a taxonomia usada para detecção de objetos. Em um nível conceitual, a API de Detecção apenas localiza objetos e seres vivos, enquanto a API de Marcação também pode incluir termos contextuais, como "interno", que não podem ser localizados com caixas delimitadoras.

## <a name="object-detection-example"></a>Exemplo de detecção de objetos

A resposta JSON a seguir ilustra o que a Pesquisa Visual Computacional retorna ao detectar objetos na imagem de exemplo.

![Uma mulher usando um dispositivo Microsoft Surface em uma cozinha](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [categorizar imagens](concept-categorizing-images.md) e [descrevendo imagens](concept-describing-images.md).