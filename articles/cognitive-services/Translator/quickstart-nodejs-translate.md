---
title: 'Início Rápido: traduzir texto – Tradução de Texto, Node.js'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você traduzirá o texto de um idioma para outro usando a API de Tradução de Texto com Node.js.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: fe81b7fffd1705cb940e3aa52f397cf0f2cf1b14
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122163"
---
# <a name="quickstart-translate-text-with-nodejs"></a>Início Rápido: Traduzir texto com Node.js

Neste início rápido, você traduz o texto de um idioma para outro usando a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Solicitação de Traduzir

O código a seguir converte o texto de origem de um idioma para outro usando o método [Traduzir](./reference/v3-0-translate.md).

1. Crie um novo projeto Node.js em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/translate?api-version=3.0';

// Translate to German and Italian.
let params = '&to=de&to=it';

let text = 'Hello, world!';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Translate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Translate (content);
```

## <a name="translate-response"></a>Resposta de Traduzir

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo identificação de transliteração e idioma, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
