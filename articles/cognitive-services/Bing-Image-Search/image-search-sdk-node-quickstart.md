---
title: 'Início Rápido: solicitar e filtrar imagens usando o SDK em Node.js'
description: Neste início rápido, você solicita e filtra as imagens retornadas pela Pesquisa de Imagem do Bing com o uso de Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929968"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Início Rápido: solicitar e filtrar imagens usando o SDK e Node.js

O SDK da Pesquisa de Imagem do Bing contém a funcionalidade da API REST para consultas de imagem e resultados de análise. 

O [código-fonte para amostras de SDK de Pesquisa de Imagem do Bing em Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar um aplicativo de console usando o SDK da Pesquisa de Imagem do Bing, execute `npm install azure-cognitiveservices-imagesearch` no ambiente de desenvolvimento.

## <a name="image-search-client"></a>Cliente de Pesquisa de Imagem
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Crie uma instância de `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Em seguida, instancie o cliente:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Use o cliente para pesquisar com um texto de consulta, neste caso, 'El Capitan':
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)