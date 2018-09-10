---
title: Ponto de extremidade da Pesquisa de Notícias do Bing | Microsoft Docs
description: Resumo do ponto de extremidade da API de Pesquisa de Notícias.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363394"
---
# <a name="news-search-endpoints"></a>Ponto de extremidade da Pesquisa de Notícias
A **API de Pesquisa de Notícias** retorna artigos de notícias, páginas da Web, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades contêm informações de resumo sobre uma pessoa, lugar ou tópico. 
## <a name="endpoints"></a>Pontos de extremidade
Para obter resultados de pesquisa de notícias, usando a API do Bing, envie uma solicitação `GET` para um dos seguintes pontos de extremidade. Os cabeçalhos e parâmetros de URL definem mais especificações.

**Ponto de extremidade 1:** retorna os itens de notícias com base na consulta de pesquisa do usuário. Se a consulta de pesquisa estiver vazia, a chamada retornará os principais artigos de notícias. A opção `?q=""` da consulta também pode ser usada com a URL `/news`. Para disponibilidade, consulte [Países e mercados com suporte](supported-countries-markets.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**Ponto de extremidade 2:** retorna os principais itens de notícias por categoria. Você pode solicitar especificamente os principais artigos de negócios, esportes ou entretenimento usando `category=business`, `category=sports`, ou `category=entertainment`.  O parâmetro `category` só pode ser usado com a URL `/news`. Há alguns requisitos formais para especificar as categorias; consulte `category` na documentação [parâmetro de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Para disponibilidade, consulte [Países e mercados com suporte](supported-countries-markets.md#supported-markets-for-news-endpoint). 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Ponto de extremidade 3:** retorna tópicos de notícias que estão atualmente tendências em redes sociais. Quando a opção `/trendingtopics` estiver incluída, a pesquisa do Bing ignora vários outros parâmetros, como `freshness` e `?q=""`. Para disponibilidade, consulte [Países e mercados com suporte](supported-countries-markets.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
## <a name="response-json"></a>Resposta JSON
A resposta a uma solicitação de pesquisa de notícias inclui resultados como objetos JSON. Os resultados de análise requer procedimentos que lidam com elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) e [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** oferecem suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade oferecem suporte a consultas que retornam um idioma específico e/ou o local por raio de pesquisa, latitude e longitude.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Notícias, consulte [Início rápido sobre pesquisa de notícias do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).