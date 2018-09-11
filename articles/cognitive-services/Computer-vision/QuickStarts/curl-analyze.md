---
title: Início rápido de cURL da API de Pesquisa Visual Computacional – analisar uma imagem remota | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você analisa uma imagem remota usando a Pesquisa Visual Computacional com cURL nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768841"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>Início Rápido: Analisar uma imagem remota – REST, cURL

Neste início rápido, você analisa uma imagem remota para extrair recursos visuais usando a Pesquisa Visual Computacional. Para analisar uma imagem local, veja [Analisar imagem local com cURL](curl-disk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-remote-image"></a>Analisar uma imagem remota

Com o [método Analisar Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), você pode extrair recursos visuais com base no conteúdo de imagem. Você pode carregar uma imagem ou especificar uma URL de imagem e escolher quais recursos retornar, incluindo:

* Uma lista detalhada de marcas relacionadas ao conteúdo da imagem.
* Uma descrição do conteúdo de imagem em uma frase completa.
* As coordenadas, o gênero e a idade de qualquer face contida na imagem.
* O ImageType (clip-art ou desenho de linha).
* A cor dominante, a cor de ênfase ou se uma imagem é em preto e branco.
* A categoria definida nessa [taxonomia](../Category-Taxonomy.md).
* A imagem contém conteúdo adulto ou sexualmente sugestivo?

Para executar a amostra, siga estas etapas:

1. Copie o seguinte código para um editor.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere a URL da Solicitação (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) para usar o local do qual você obteve suas chaves de assinatura, se necessário.
1. Opcionalmente, altere a imagem (`{\"url\":\"...`) a analisar.
1. Opcionalmente, altere o idioma de resposta (`language=en`).
1. Abra uma janela Comando em um computador com a cURL instalada.
1. Cole o código na janela e execute o comando.

>[!NOTE]
>Você deve usar o mesmo local em sua chamada REST que é usado para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura de westus, substitua "westcentralus" na URL abaixo por "westus".

## <a name="analyze-image-request"></a>Analisar a solicitação da Imagem

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="analyze-image-response"></a>Analisar a resposta da Imagem

Uma resposta bem-sucedida é retornada em JSON, por exemplo:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos. Para experimentar rapidamente as APIs de Pesquisa Visual Computacional, tente o [Console de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
