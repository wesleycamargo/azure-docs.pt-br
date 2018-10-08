---
title: Pontos de extremidade de pesquisa de vídeo – Pesquisa de Vídeo do Bing
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: rosh
ms.openlocfilehash: c153f577f76944d22f9a1b0fb4b24d332d2a02c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220764"
---
# <a name="video-search-endpoints"></a>Ponto de extremidade de Pesquisa de Vídeo
A **API de Pesquisa de Vídeo** inclui três pontos de extremidade.  Ponto de extremidade 1 retorna vídeos da Web com base em uma consulta. Ponto de extremidade 2 retorna informações sobre um vídeo com base no parâmetro de URL `modules`.  O ponto de extremidade 3 retorna imagens populares.

## <a name="endpoints"></a>Pontos de extremidade
Para obter resultados vídeos usando a API do Bing, envie uma solicitação `GET` para um dos seguintes pontos de extremidade. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade 1:** retorna vídeos que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Ponto de extremidade 2:** retorna informações sobre um vídeo, como vídeos relacionados. Inclua o `modules` [parâmetro de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), que é uma lista delimitada por vírgulas de informações para a solicitação.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Ponto de extremidade 3:** retorna vídeos que são populares com base nas solicitações de pesquisa feitas por outros usuários. Os vídeos são separados em categorias diferentes, por exemplo, com base em eventos ou pessoas notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).
## <a name="response-json"></a>Resposta JSON
A resposta a uma solicitação de pesquisa de vídeos inclui resultados como objetos JSON. Para obter exemplos de como analisar os resultados, confira o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) e o [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Vídeo, consulte [Início rápido sobre pesquisa de vídeo do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).