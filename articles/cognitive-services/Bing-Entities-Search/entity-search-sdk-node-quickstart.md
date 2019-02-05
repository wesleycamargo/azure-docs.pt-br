---
title: 'Início rápido: SDK de Pesquisa de Entidade do Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configuração para aplicativo de console do SDK da Pesquisa de Entidade com Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 0a61a6b1ada68307af7e7e574cba9910841f5939
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153004"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Início Rápido: SDK de Pesquisa de Entidade do Bing com Node

O SDK da Pesquisa de Entidade do Bing contém a funcionalidade da API REST para consultas de entidade e resultados de análise. 

O [código-fonte para exemplos de SDK da Pesquisa de Entidade do Bing C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) está disponível no Git Hub.
## <a name="application-dependencies"></a>Dependências de aplicativo
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Para configurar um aplicativo de console usando o SDK de Pesquisa de Entidade do Bing:
* Execute `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
* Execute `npm install azure-cognitiveservices-entitysearch` no seu ambiente de desenvolvimento.

## <a name="entity-search-client"></a>Cliente da Pesquisa de Entidade
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Crie uma instância de `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Em seguida, instancie o cliente e pesquise os resultados:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
O código imprime `result.value` itens para o console sem analisar qualquer texto.  Os resultados por categoria, se houver, incluirão:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
