---
title: 'Início Rápido: SDK de Pesquisa de Notícias do Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configurar o aplicativo de console do SDK de Pesquisa de Notícias do Bing
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 075ce771b6fa92663f172734aabf57b089b294a9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316932"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Início Rápido: SDK de Pesquisa de Notícias do Bing com Node

O SDK de Pesquisa de Notícias do Bing contém a funcionalidade da API REST para consultas e resultados de análise. 

O [código-fonte para amostras de SDK de Pesquisa de Notícias do Bing em Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Preços dos Serviços Cognitivos - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/). 

Para configurar um aplicativo de console usando o SDK de Pesquisa de Notícias do Bing:
* Execute `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
* Execute `npm install azure-cognitiveservices-newssearch` no seu ambiente de desenvolvimento.

## <a name="news-search-client"></a>Cliente de Pesquisa de Notícias
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Crie uma instância de `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Em seguida, instancie o cliente:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Use o cliente para pesquisar com um texto de consulta, neste caso 'Olimpíadas de Inverno':
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
O código imprime `result.value` itens para o console sem analisar qualquer texto. Os resultados por categoria, se houver, incluirão:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
