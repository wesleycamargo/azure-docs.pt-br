---
title: Início rápido do Node do SDK da Pesquisa na Web | Microsoft Docs
description: Configuração para aplicativo de console do SDK de Pesquisa na Web.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364657"
---
# <a name="web-search-sdk-node-quickstart"></a>Início rápido do Node do SDK da Pesquisa na Web

O SDK da Pesquisa na Web do Bing contém a funcionalidade da API REST para consultas na Web e resultados da análise.

O [código-fonte para amostras de SDK de Pesquisa na Web do Bing em Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar um aplicativo de console usando o SDK da Pesquisa na Web do Bing, execute `npm install azure-cognitiveservices-websearch` no seu ambiente de desenvolvimento.

## <a name="web-search-client"></a>Cliente de pesquisa na Web
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Crie uma instância de `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Em seguida, instancie o cliente:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Pesquise resultados:
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
O código imprime `result.value` itens para o console sem analisar qualquer texto.  Os resultados por categoria, se houver, incluirão:
- _type: 'ImageObject'
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
