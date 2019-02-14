---
title: 'Início Rápido: Pesquisa de Resposta do Projeto, Node'
description: Introdução ao uso da Pesquisa de Resposta de Projeto com Node.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 7a749efca0e8acacff4dac49e7a524d25ef5acad
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878571"
---
# <a name="quickstart-project-answer-search-with-node"></a>Início Rápido: Pesquisa de Resposta do Projeto com Node

O exemplo de Node a seguir cria uma consulta para obter informações sobre o Parque Nacional de Yosemite.

## <a name="prerequisites"></a>Pré-requisitos

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

Este exemplo usa o Node v8.9.4

## <a name="code-scenario"></a>Cenário do código 

O código a seguir obtém as respostas.
Ele é implementado nas etapas a seguir:
1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a URL de consulta para visualizar e adicione o parâmetro de consulta.  
3. Crie uma função de manipulador para a resposta.
4. Defina a função Search que cria a solicitação e adicione o cabeçalho *Ocp-Apim-Subscription-Key*.
5. Execute a função Search. 

O código completo para esta demonstração é:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Próximas etapas
- [Exemplo de código em C#](c-sharp-quickstart.md)
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em Python](python-quickstart.md)
