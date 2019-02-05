---
title: 'Início Rápido: API de Verificação Ortográfica do Bing, PHP'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Verificação Ortográfica do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 156c4414bbf7bda13e61c065e7b7100f6ae773b1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166179"
---
# <a name="quickstart-for-bing-spell-check-api-with-php"></a>Início Rápido para a API de Verificação Ortográfica do Bing com PHP 

Este artigo mostra como usar a [API de Verificação Ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)  com PHP. A API de Verificação Ortográfica retorna uma lista de palavras que ela não reconhece, juntamente com as substituições sugeridas. Normalmente, você enviaria texto para essa API e faria as substituições sugeridas no texto, ou então as mostraria para o usuário do seu aplicativo para que ele pudesse decidir realizar ou não as substituições. Este artigo mostra como enviar uma solicitação que contém o texto "Omá, mndo!" As substituições sugeridas são "Olá" e "mundo".

## <a name="prerequisites"></a>Pré-requisitos

Você precisará ter o [PHP 5.6.x](http://php.net/downloads.php) para executar esse código.

É necessário ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **API de Verificação Ortográfica do Bing v7**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/#lang) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Obter resultados da Verificação Ortográfica

1. Crie um projeto PHP em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// These properties are used for optional headers (see below).
// define("CLIENT_ID", "<Client ID from Previous Response Goes Here>");
// define("CLIENT_IP", "999.999.999.999");
// define("CLIENT_LOCATION", "+90.0000000000000;long: 00.0000000000000;re:100.000000000000");

$host = 'https://api.cognitive.microsoft.com';
$path = '/bing/v7.0/spellcheck?';
$params = 'mkt=en-us&mode=proof';

$input = "Hollo, wrld!";

$data = array (
    'text' => urlencode ($input)
);

// NOTE: Replace this example key with a valid subscription key.
$key = 'ENTER KEY HERE';

// The following headers are optional, but it is recommended
// that they are treated as required. These headers will assist the service
// with returning more accurate results.
//'X-Search-Location' => CLIENT_LOCATION
//'X-MSEdge-ClientID' => CLIENT_ID
//'X-MSEdge-ClientIP' => CLIENT_IP

$headers = "Content-type: application/x-www-form-urlencoded\r\n" .
    "Ocp-Apim-Subscription-Key: $key\r\n";

// NOTE: Use the key 'http' even if you are making an HTTPS request. See:
// http://php.net/manual/en/function.stream-context-create.php
$options = array (
    'http' => array (
        'header' => $headers,
        'method' => 'POST',
        'content' => http_build_query ($data)
    )
);
$context  = stream_context_create ($options);
$result = file_get_contents ($host . $path . $params, false, $context);

if ($result === FALSE) {
    /* Handle error */
}

$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
echo $json;
?>
```

**Resposta**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de Verificação Ortográfica do Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Consulte também

- [Visão geral da Verificação Ortográfica do Bing](../proof-text.md)
- [Referência de API de Verificação Ortográfica do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
