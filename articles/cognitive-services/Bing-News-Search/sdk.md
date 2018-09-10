---
title: SDK de Pesquisa do Bing | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: SDK de Pesquisa do Bing para aplicativos que pesquisam na Web.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364647"
---
# <a name="bing-search-sdk"></a>SDK de Pesquisa do Bing
As amostras da API de Pesquisa de Notícias do Bing incluem cenários que:
1. Consultam notícias para pesquisar termos com os parâmetros `market` e `count`, verificam o número de resultados e imprimem `totalEstimatedMatches`, nome, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
2. Consultam as notícias mais recentes para pesquisar termos com os parâmetros `freshness` e `sortBy`, verificam o número de resultados e imprimem `totalEstimatedMatches`, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
3. Consultam notícias de categoria para `movie` e `TV entertainment` com pesquisa segura, verificam o número de resultados e imprimem categoria, nome, URL, descrição, hora de publicação e nome do provedor do primeiro resultado de notícias.
4. Consultam tópicos populares de notícias no Bing, verificam o número de resultados e imprimem nome, texto da consulta, `webSearchUrl`, `newsSearchUrl` e URL de imagem do primeiro resultado de notícias.

Os SDKs de Pesquisa do Bing tornam a funcionalidade de pesquisa na Web facilmente acessível nas linguagens de programação a seguir:
* Introdução às [amostras do .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Confira também [Bibliotecas .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) para obter definições e dependências.
* Introdução às [amostras do Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Confira também [Bibliotecas Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) para obter definições e dependências.
* Introdução às [amostras do Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Confira também [Bibliotecas Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) para obter definições e dependências.
* Introdução às [amostras do Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Confira também [Bibliotecas Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) para obter definições e dependências.

As amostras de SDK de cada linguagem incluem um arquivo Leiame com detalhes sobre os pré-requisitos e a instalação/execução das amostras.