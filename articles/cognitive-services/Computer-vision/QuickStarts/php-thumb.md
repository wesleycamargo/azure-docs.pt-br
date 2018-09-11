---
title: Início rápido de PHP da API de Pesquisa Visual Computacional – criar miniatura | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você pode gerar uma miniatura de uma imagem usando a Pesquisa Visual Computacional com PHP nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 7170e469d042c4406a555fddaa25bff53236f365
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768839"
---
# <a name="quickstart-generate-a-thumbnail---rest-php"></a>Início Rápido: Gerar uma miniatura – REST, PHP

Neste início rápido, você gerará uma miniatura de uma imagem usando Pesquisa Visual Computacional.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Solicitação Obter Miniatura

Com o [método Obter Miniatura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), você pode gerar uma miniatura de uma imagem. Você especifica a altura e largura, que podem ser diferentes da proporção da imagem de entrada. A Pesquisa Visual Computacional usa o corte inteligente para identificar a região de interesse de modo inteligência e gerar as coordenadas de corte com base nessa região.

Para executar a amostra, siga estas etapas:

1. Copie o seguinte código para um editor.
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere `uriBase` para usar o local do qual você obteve suas chaves de assinatura, se necessário.
1. Opcionalmente, defina `imageUrl` para a imagem que você deseja analisar.
1. Salve o arquivo com uma extensão `.php`.
1. Abra o arquivo em uma janela de navegador com suporte para PHP.

Esta amostra usa o pacote PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2).

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
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

## <a name="get-thumbnail-response"></a>Resposta de Obter Miniatura

Uma resposta bem-sucedida contém o binário da imagem em miniatura. Se a solicitação falhar, a resposta conterá um código de erro e uma mensagem para ajudar a determinar o que deu errado.

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos. Para experimentar rapidamente as APIs de Pesquisa Visual Computacional, tente o [Console de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar APIs de Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
