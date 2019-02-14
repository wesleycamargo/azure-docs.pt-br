---
title: Detecção de marca – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de marca/logotipo usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a5cf4a2b0f5e2e2c83e9fe6aa9237c943fa23ed9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864790"
---
# <a name="brand-detection"></a>Detecção de marca

A detecção de marca é um modo especializado de [detecção de objeto](concept-object-detection.md) que usa um banco de dados de milhares de logotipos globais para identificar marcas comerciais em imagens ou vídeo. Você pode usar esse recurso, por exemplo, para descobrir quais marcas são mais populares em mídia social ou mais predominantes no posicionamento de produto de mídia.

O serviço de Pesquisa Visual Computacional detecta se há logotipos da marca em uma determinada imagem; quando há, ele retorna o nome da marca, uma pontuação de confiança e as coordenadas de uma caixa delimitadora em torno do logotipo.

O banco de dados interno do logotipo aborda marcas populares em aparelhos eletrônicos, roupas e muito mais. Se você acha que a marca que você está procurando não foi detectada pelo serviço de Pesquisa Visual Computacional, pode ser melhor criar e treinar seu próprio detector de logotipos usando o serviço de [Visão Personalizada](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Exemplo de detecção de marca

As respostas JSON a seguir ilustram o que a Pesquisa Visual Computacional retorna ao detectar marcas nas imagens de exemplo.

![Um agasalho leve cinza com um rótulo e o logotipo da Microsoft nele](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
Em alguns casos, o detector de marca selecionará a imagem do logotipo e o nome da marca estilizado como dois logotipos separados.

![Uma camiseta vermelha com um rótulo e o logotipo da Microsoft nele](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Usar a API
O recurso de detecção de marca faz parte da API [Analisar Imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Quando você receber a resposta JSON completa, simplesmente analise a cadeia de caracteres para o conteúdo da seção `"brands"`.

* [Início Rápido: Analisar uma imagem (SDK do .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Início Rápido: Analisar uma imagem (API REST)](./quickstarts/csharp-analyze.md)