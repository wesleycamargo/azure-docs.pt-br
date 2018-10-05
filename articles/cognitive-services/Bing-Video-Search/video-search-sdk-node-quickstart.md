---
title: 'Início Rápido: SDK da Pesquisa de Vídeo do Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configuração para o aplicativo de console do SDK da Pesquisa de Vídeo do Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225609"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Início Rápido: SDK da Pesquisa de Vídeo do Bing com o Node

O SDK da Pesquisa de Vídeo do Bing contém a funcionalidade da API REST para consultas de vídeo e resultados de análise. 

O [código-fonte para exemplos de SDK da Pesquisa de Vídeo do Bing](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) está disponível no Git Hub.

## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar um aplicativo de console usando o SDK da Pesquisa de Vídeo do Bing, execute `npm install azure-cognitiveservices-videosearch` no ambiente de desenvolvimento.

## <a name="video-search-client"></a>Cliente da Pesquisa de Vídeo
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Crie uma instância de `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Em seguida, instancie o cliente:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Pesquise resultados.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
