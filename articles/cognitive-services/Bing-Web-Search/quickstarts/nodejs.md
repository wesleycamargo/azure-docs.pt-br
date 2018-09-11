---
title: 'Início Rápido: usar Node.js para chamar a API de Pesquisa na Web do Bing'
description: Neste início rápido, você aprenderá a fazer sua primeira chamada à API de Pesquisa na Web do Bing usando Node.js e receber uma resposta JSON.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 7a46500f7cbf319c788761bccfaa92197ef67490
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886924"
---
# <a name="quickstart-use-nodejs-to-call-the-bing-web-search-api"></a>Início Rápido: usar Node.js para chamar a API de Pesquisa na Web do Bing  

Use este início rápido para fazer sua primeira chamada à API de Pesquisa na Web do Bing e receber uma resposta JSON em menos de 10 minutos.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão alguns itens de que você poderá precisar antes de executar este início rápido:

* [Node.js 6](https://nodejs.org/en/download/) ou posterior
* Uma chave de assinatura  

## <a name="create-a-project-and-declare-required-modules"></a>Criar um projeto e declarar os módulos necessários

Crie um projeto Node.js em seu IDE ou editor favorito. Em seguida, copie o snippet de código abaixo para seu projeto. Este início rápido usa o modo estrito e requer o módulo `https` para enviar e receber dados.

```javascript
// Use strict mode.
'use strict';

// Require the https module.
let https = require('https');
```

## <a name="define-variables"></a>Definir variáveis

Algumas variáveis devem ser definidas antes de podermos continuar. Confirme que `host` e `path` são válidos e substitua o valor `subscriptionKey` por uma chave de assinatura válida da sua conta do Azure. Fique à vontade para personalizar a consulta de pesquisa substituindo o valor para `term`.

```javascript
// Replace with a valid subscription key.
let subscriptionKey = 'enter key here';

/*
 * Verify the endpoint URI. If you
 * encounter unexpected authorization errors, double-check this host against
 * the endpoint for your Bing Web search instance in your Azure dashboard.  
 */
let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/search';
let term = 'Microsoft Cognitive Services';

// Validate the subscription key.
if (subscriptionKey.length === 32) {
    bing_web_search(term);
} else {
    console.log('Invalid Bing Search API subscription key!');
    console.log('Please paste yours into the source code.');
}
```

## <a name="create-a-response-handler"></a>Criar um manipulador de resposta

Crie um manipulador para stringify e analise a resposta. O `response_handler` é chamado sempre que uma solicitação é feita à API de Pesquisa na Web do Bing, como você verá na próxima seção.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        console.log('\nRelevant Headers:\n');
        for (var header in response.headers)
            // Headers are lowercased by Node.js.
            if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                 console.log(header + ": " + response.headers[header]);
        // Stringify and parse the response body.
        body = JSON.stringify(JSON.parse(body), null, '  ');
        console.log('\nJSON Response:\n');
        console.log(body);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};
```

## <a name="make-a-request-and-print-the-response"></a>Fazer uma solicitação e imprimir a resposta

Construa a solicitação e faça uma chamada à API de Pesquisa na Web do Bing. Depois que a solicitação tiver sido feita, a função `response_handler` será chamada e a resposta será impressa.

```javascript
let bing_web_search = function (search) {
    console.log('Searching the Web for: ' + term);
        // Declare the method, hostname, path, and headers.
        let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
    // Request to the Bing Web Search API.
    let req = https.request(request_params, response_handler);
    req.end();
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é executar seu código! Se você quiser comparar seu código com o nosso, o [exemplo de código está disponível no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="sample-response"></a>Resposta de exemplo

As respostas da API de Pesquisa na Web do Bing são retornadas como JSON. Este exemplo de resposta foi truncado para mostrar um único resultado.  

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
