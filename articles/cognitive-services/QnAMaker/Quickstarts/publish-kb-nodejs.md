---
title: Publicar a base de dados de conhecimento – Início Rápido do Node.js – Serviços Cognitivos do Azure | Microsoft Docs
description: Como publicar uma base de dados de conhecimento em Node.js para o QnA Maker.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: nolachar
ms.openlocfilehash: fad3857774f2726e3db595ee6bc72d52a8ac46d9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "43768522"
---
# <a name="publish-a-knowledge-base-in-nodejs"></a>Como publicar uma base de dados de conhecimento em Node.js

O código a seguir publica uma base de dados de conhecimento existente usando o método [Publicar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe).

Se você não tiver uma base de dados de conhecimento, pode criar uma de amostra para usar para este início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-nodejs.md).

1. Crie um projeto Node em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Substitua o valor `subscriptionKey` por uma chave de assinatura válida.
1. Substitua o valor `kb` por uma ID de base de dados de conhecimento válida. Encontre esse valor acessando uma das suas [bases de dados de conhecimento do QnA Maker](https://www.qnamaker.ai/Home/MyServices). Selecione a base de dados de conhecimento que você deseja publicar. Uma vez nessa página, localize o 'kdid=' na URL, conforme mostrado abaixo. Use seu valor para o seu exemplo de código.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)
1. Execute o programa.

``` Node.js
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases POST method.
let publish_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
// Extract the data we want from the POST response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

var path = service + method + kb;
publish_kb (path, '', function (result) {
    console.log (pretty_print(result));
});
```

## <a name="understand-what-qna-maker-returns"></a>Entender o que o QnA Maker retorna

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)