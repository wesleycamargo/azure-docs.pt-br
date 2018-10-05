---
title: 'Início Rápido: API do Node.js – Criar uma base de dados de conhecimento – QnA Maker'
description: Este início rápido orienta você pela criação programática de um exemplo de base de dados de conhecimento de QnA Maker, que será exibido no painel do Azure da sua conta da API dos serviços Cognitivos.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 435c937e64d0befc5a3bf30f9d58ccd303247b9e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037296"
---
# <a name="create-a-new-knowledge-base-in-nodejs"></a>Criar uma nova base de dados de conhecimento em Node.js

Neste início rápido orienta você pela criação de um exemplo de base de dados de Conhecimento de QnA Maker, programaticamente, que será exibido no painel do Azure da sua conta da API dos serviços Cognitivos.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

Dois exemplos de URLs de perguntas frequentes são apresentados abaixo (em 'urls' de **req={}**) que fornecerão conteúdo. O QnA Maker extrai automaticamente perguntas e respostas do conteúdo semiestruturado, conforme explicado neste documento de [fontes de dados](../Concepts/data-sources-supported.md). Você também pode usar seus próprio URLs de perguntas frequentes neste guia de início rápido.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Node.js 6+](https://nodejs.org/en/download/) para executar esse código.

É necessário ter uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de QnA Maker da Microsoft**. Você precisará de uma chave de assinatura paga no [painel do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices). Qualquer uma das chaves funcionará para este início rápido.

![Chave de serviço do painel do Azure](../media/sub-key.png)

Para obter mais ajuda com o Visual Studio e o Node.js: [Início Rápido: usar o Visual Studio para criar seu primeiro aplicativo Node.js](https://docs.microsoft.com/en-us/visualstudio/ide/quickstart-nodejs).

## <a name="create-knowledge-base"></a>Criar base de dados de conhecimento

O código a seguir cria uma nova base de dados conhecimento, usando o método [Criar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de assinatura válida.
4. Execute o programa.

```nodejs
'use strict';

let fs = require('fs');
let https = require('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE';

// Components used to create HTTP request URIs for QnA Maker operations.
let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

// Formats and indents JSON for display.
let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
};

// The 'callback' is called from the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        // Call the 'callback' with the status code, headers, and body of the response.
        callback({ status: response.statusCode, headers: response.headers, body: body });
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls 'callback' from the entire response.
let get_response_handler = function (callback) {
    // Return a function that takes an HTTP response and is closed over the specified callback.
    // This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler(callback, response);
    };
};

// Call 'callback' when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method: 'POST',
        hostname: host,
        path: path,
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': content.length,
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the 'callback' function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.write(content);
    req.end();
};

// Call 'callback' when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.end();
};

// Call 'callback' when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the POST request.
    post(path, req, function (response) {
        // Extract the data we want from the POST response and pass it to the callback function.
        callback({ operation: response.headers.location, response: response.body });
    });
};

// Call 'callback' when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the GET request.
    get(path, function (response) {
        // Extract the data we want from the GET response and pass it to the callback function.
        callback({ wait: response.headers['retry-after'], response: response.body });
    });
};

// Dictionary that holds the knowledge base.
// The data source includes a QnA pair with metadata, the URL for the
// QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
let req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
        {
            "id": 0,
            "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
            "source": "Custom Editorial",
            "questions": [
                "How do I programmatically update my Knowledge Base?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ],
    "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
};

// Build your path URL.
var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb(path, content, function (result) {
    // Formats and indents the JSON response from the /knowledgebases/create method for display.
    console.log(pretty_print(result.response));
    // Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
        // Check the status of the operation.
        check_status(path, function (status) {
            // Formats and indents the JSON for display.
            console.log(pretty_print(status.response));
            // Convert the status into an object and get the value of the 'operationState'.
            var state = (JSON.parse(status.response)).operationState;
            // If the operation isn't complete, wait and query again.
            if (state === 'Running' || state === 'NotStarted') {
                console.log('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    };
    // Begin the loop.
    loop();
});
```

## <a name="understand-what-qna-maker-returns"></a>Entender o que o QnA Maker retorna

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo. Seus resultados podem ser um pouco diferentes. Se a chamada final retornar um estado "Bem-sucedido", isso indica que sua base de dados de conhecimento foi criada com sucesso. Para solucionar problemas, consulte [Detalhes da operação Get](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) da API de QnA Maker.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)