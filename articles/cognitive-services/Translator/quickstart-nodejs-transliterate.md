---
title: Tradução de Texto – converter script de texto com Node.js | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você converter texto em um idioma de um script para outro usando a API de Tradução de Texto com Node.js nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: b16af911e5822deaa7cc7bcfe792245ae154eb26
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "43768414"
---
# <a name="quickstart-transliterate-text-with-nodejs"></a>Início Rápido: Transliterar texto com Node.js

Neste início rápido, você converter texto em um idioma de um script para outro usando a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Solicitação de Transliterar

O seguinte converte texto em um idioma de um script em outro usando o método [Transliterar](./reference/v3-0-transliterate.md).

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
let path = '/transliterate?api-version=3.0';

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
let params = '&language=ja&fromScript=jpan&toScript=latn';

// Transliterate "good afternoon".
let text = 'こんにちは';

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

let Transliterate = function (content) {
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

Transliterate (content);
```

## <a name="transliterate-response"></a>Resposta de Transliterar

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo tradução e identificação de idioma, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
