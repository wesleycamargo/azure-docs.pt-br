---
title: Início rápido do Node do SDK da Pesquisa de Entidade | Microsoft Docs
description: Configuração para aplicativo de console do SDK da Pesquisa de Entidade.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364803"
---
# <a name="entity-search-sdk-node-quickstart"></a>Guia de início rápido do Node do SDK da Pesquisa de Entidade

O SDK da Pesquisa de Entidade do Bing contém a funcionalidade da API REST para consultas de entidade e resultados de análise. 

O [código-fonte para exemplos de SDK da Pesquisa de Entidade do Bing C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) está disponível no Git Hub.
## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar um aplicativo de console usando o SDK da Pesquisa de Entidade do Bing, execute `npm install azure-cognitiveservices-entitysearch` no ambiente de desenvolvimento.

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
- _tipo: 'Thing'
- _tipo: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Exemplos de SDK do Node.js dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)