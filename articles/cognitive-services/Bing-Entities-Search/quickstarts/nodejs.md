---
title: Início rápido do Node.JS para Serviços Cognitivos do Azure, API de Pesquisa de Entidade do Bing | Microsoft Docs
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Pesquisa de Entidade do Bing nos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 7396976453c240d5ea9a767f8c26ac96d10d1e1f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364507"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-nodejs"></a>Início rápido para API de Pesquisa de Entidade do Microsoft Bing com Node.JS 
<a name="HOLTop"></a>

Este artigo mostra como usar a API de [Pesquisa de Entidade do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) com Node.JS.

## <a name="prerequisites"></a>pré-requisitos

É necessário ter o [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

É necessário ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **API de Pesquisa de Entidade do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.

## <a name="search-entities"></a>Pesquisar entidades

Para executar esse aplicativo, siga estas etapas.

1. Crie um novo projeto Node.JS no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```nodejs
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/entities';

let mkt = 'en-US';
let q = 'italian restaurant near me';

let params = '?mkt=' + mkt + '&q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

Search ();
```

**Resposta**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial da Pesquisa de Entidade do Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Visão geral da Pesquisa de Entidade do Bing](../search-the-web.md )
> [Referência de API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
