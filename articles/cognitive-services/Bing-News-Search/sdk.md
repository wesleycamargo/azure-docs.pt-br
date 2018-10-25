---
title: SDK de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: SDK de Pesquisa de Notícias do Bing para aplicativos que pesquisam na Web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801226"
---
# <a name="bing-search-sdk"></a>SDK de Pesquisa do Bing
As amostras da API de Pesquisa de Notícias do Bing incluem cenários que:
1. Consultam notícias para pesquisar termos com os parâmetros `market` e `count`, verificam o número de resultados e imprimem `totalEstimatedMatches`, nome, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
2. Consultam as notícias mais recentes para pesquisar termos com os parâmetros `freshness` e `sortBy`, verificam o número de resultados e imprimem `totalEstimatedMatches`, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
3. Consultam notícias de categoria para `movie` e `TV entertainment` com pesquisa segura, verificam o número de resultados e imprimem categoria, nome, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
4. Consultam tópicos populares de notícias no Bing, verificam o número de resultados e imprimem nome, texto da consulta, `webSearchUrl`, `newsSearchUrl` e URL de imagem do primeiro resultado de notícias.

Os SDKs de Pesquisa do Bing tornam a funcionalidade de pesquisa na Web facilmente acessível nas linguagens de programação a seguir:
* Introdução aos [exemplos .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Consulte também as [bibliotecas do .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) para obter definições e dependências.
* Introdução aos [exemplos Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Consulte também as [bibliotecas do Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) para obter definições e dependências.
* Introdução aos [exemplos Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Consulte também as [bibliotecas do Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) para obter definições e dependências.
* Introdução aos [exemplos Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Consulte também as [bibliotecas do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) para obter definições e dependências.

As amostras de SDK de cada linguagem incluem um arquivo Leiame com detalhes sobre os pré-requisitos e a instalação/execução dos exemplos.