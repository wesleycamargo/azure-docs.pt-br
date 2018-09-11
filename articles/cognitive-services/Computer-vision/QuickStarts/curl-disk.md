---
title: Início rápido de cURL da API de Pesquisa Visual Computacional – analisar uma imagem local | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você analisa uma imagem local usando a Pesquisa Visual Computacional com cURL nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768860"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Início Rápido: Analisar uma imagem local – REST, cURL

Neste início rápido, você analisa uma imagem local para extrair recursos visuais usando a Pesquisa Visual Computacional. Para analisar uma imagem remota, veja [Analisar uma imagem remota com cURL](curl-analyze.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-local-image"></a>Analisar uma imagem local

Este exemplo é semelhante a [Analisar uma imagem remota com cURL](curl-analyze.md), exceto que a imagem a analisar é lida localmente do disco. Três alterações são necessárias:

- Altere o Tipo de Conteúdo para `"Content-Type: application/octet-stream"`.
- Altere a opção `-d` para `--data-binary`.
- Especifique a imagem a analisar usando a seguinte sintaxe: `@C:/Pictures/ImageToAnalyze.jpg`.

Para executar a amostra, siga estas etapas:

1. Copie o seguinte código para um editor.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere a URL da Solicitação (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) para usar o local do qual você obteve suas chaves de assinatura, se necessário.
1. Substitua `<Image To Analyze>` pela imagem local que você deseja analisar.
1. Opcionalmente, altere o idioma de resposta (`language=en`).
1. Abra uma janela Comando em um computador com a cURL instalada.
1. Cole o código na janela e execute o comando.

>[!NOTE]
>Você deve usar o mesmo local em sua chamada REST que é usado para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura de westus, substitua "westcentralus" na URL abaixo por "westus".

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
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
