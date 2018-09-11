---
title: Início rápido de PHP da API de Pesquisa Visual Computacional – analisar imagem | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você analisa uma imagem usando a Pesquisa Visual Computacional com PHP nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 114674f47ee945717e866d97ffed747ae45decc8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768571"
---
# <a name="quickstart-analyze-a-remote-image---rest-php"></a>Início Rápido: Analisar uma imagem remota – REST, PHP

Neste início rápido, você analisa uma imagem para extrair recursos visuais usando a Pesquisa Visual Computacional.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Analisar a solicitação da Imagem

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
1. Altere `uriBase` para usar o local do qual você obteve suas chaves de assinatura, se necessário.
1. Opcionalmente, defina `imageUrl` para a imagem que você deseja analisar.
1. Opcionalmente, altere o idioma de resposta (`'language' => 'en'`).
1. Salve o arquivo com uma extensão `.php`.
1. Abra o arquivo em uma janela de navegador com suporte para PHP.

Esta amostra usa o pacote PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2).

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
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
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
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
