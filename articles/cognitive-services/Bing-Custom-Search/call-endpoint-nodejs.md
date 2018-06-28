---
title: Chamar ponto de extremidade usando Node.js - Pesquisa Personalizada do Bing - Serviços Cognitivos da Microsoft
description: Este início rápido mostra como solicitar resultados de pesquisa da instância de pesquisa personalizada usando Node.js para chamar o ponto de extremidade da Pesquisa Personalizada do Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364801"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Chamar ponto de extremidade da Pesquisa Personalizada do Bing (Node.js)

Este início rápido mostra como solicitar resultados de pesquisa da instância de pesquisa personalizada usando Node.js para chamar o ponto de extremidade da Pesquisa Personalizada do Bing. 

## <a name="prerequisites"></a>pré-requisitos
Para concluir este início rápido, você precisa de:

- Uma instância de pesquisa personalizada. Consulte [Criar a primeira instância da Pesquisa Personalizada do Bing ](quick-start.md).

- [Node. js](https://www.nodejs.org/) instalado.

-  [Conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **APIs de Pesquisa do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.

## <a name="run-the-code"></a>Executar o código

Para chamar o ponto de extremidade da Pesquisa Personalizada do Bing, siga estas etapas:

1. Crie uma pasta para o código.
2. Em um prompt de comando ou terminal, navegue até a pasta que você acabou de criar.
3. Instale o módulo do nó de **solicitação**:
    <pre>
    npm install request
    </pre>
4. Crie o arquivo BingCustomSearch.js e copie o seguinte código para ele.
5. Substitua **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** com a chave e ID de configuração (consulte a etapa 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Execute o código usando os seguintes comandos.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Próximas etapas
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)