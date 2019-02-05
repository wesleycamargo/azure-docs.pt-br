---
title: 'Início Rápido: API de Verificação Ortográfica do Bing, Node.js'
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
ms.openlocfilehash: bfe37f5f754303f146328326900f23e54a034d7e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194722"
---
# <a name="quickstart-for-bing-spell-check-api-with-nodejs"></a>Início Rápido para a API de Verificação Ortográfica do Bing com Node.js 

Este artigo mostra como usar a [API de Verificação Ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)  com Node.js. A API de Verificação Ortográfica retorna uma lista de palavras que ela não reconhece, juntamente com as substituições sugeridas. Normalmente, você enviaria texto para essa API e faria as substituições sugeridas no texto, ou então as mostraria para o usuário do seu aplicativo para que ele pudesse decidir realizar ou não as substituições. Este artigo mostra como enviar uma solicitação que contém o texto "Omá, mndo!". As substituições sugeridas serão "Olá" e "mundo".

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter o [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

É necessário ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **API de Verificação Ortográfica do Bing v7**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/#lang) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Obter resultados da Verificação Ortográfica

1. Crie um projeto Node.js em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```nodejs
'use strict';

let https = require ('https');

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/spellcheck';

/* NOTE: Replace this example key with a valid subscription key (see the Prequisites section above). Also note v5 and v7 require separate subscription keys. */
let key = 'ENTER KEY HERE';

// These values are used for optional headers (see below).
// let CLIENT_ID = "<Client ID from Previous Response Goes Here>";
// let CLIENT_IP = "999.999.999.999";
// let CLIENT_LOCATION = "+90.0000000000000;long: 00.0000000000000;re:100.000000000000";

let mkt = "en-US";
let mode = "proof";
let text = "Hollo, wrld!";
let query_string = "?mkt=" + mkt + "&mode=" + mode;

let request_params = {
    method : 'POST',
    hostname : host,
    path : path + query_string,
    headers : {
        'Content-Type' : 'application/x-www-form-urlencoded',
        'Content-Length' : text.length + 5,
        'Ocp-Apim-Subscription-Key' : key,
//        'X-Search-Location' : CLIENT_LOCATION,
//        'X-MSEdge-ClientID' : CLIENT_ID,
//        'X-MSEdge-ClientIP' : CLIENT_ID,
    }
};

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        console.log (body);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
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
