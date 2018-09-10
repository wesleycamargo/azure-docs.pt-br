---
title: Ponto de extremidade de Pesquisa de Vídeo | Microsoft Docs
description: Resumo do ponto de extremidade da API de Pesquisa de Vídeo.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363390"
---
# <a name="video-search-endpoints"></a>Ponto de extremidade de Pesquisa de Vídeo
A **API de Pesquisa de Vídeo** inclui três pontos de extremidade.  Ponto de extremidade 1 retorna vídeos da Web com base em uma consulta. Ponto de extremidade 2 retorna informações sobre um vídeo com base no parâmetro de URL `modules`.  Ponto de extremidade 3 retorna imagens populares.

## <a name="endpoints"></a>Pontos de extremidade
Para obter resultados vídeos usando a API do Bing, envie uma solicitação `GET` para um dos seguintes pontos de extremidade. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade 1:** retorna vídeos que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Ponto de extremidade 2:** retorna informações sobre um vídeo, como vídeos relacionados. Inclua o `modules`parâmetro de consulta[ ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters), que é uma lista delimitada por vírgulas de informações para a solicitação.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Ponto de extremidade 3:** retorna vídeos que são populares com base nas solicitações de pesquisa feitas por outros usuários. Os vídeos são separados em categorias diferentes, por exemplo, com base em eventos ou pessoas notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).
## <a name="response-json"></a>Resposta JSON
A resposta a uma solicitação de pesquisa de vídeos inclui resultados como objetos JSON. Para obter exemplos de análise de resultados, consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) e [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** oferecem suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade oferecem suporte a consultas que retornam um idioma específico e/ou o local por raio de pesquisa, latitude e longitude.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Vídeo, consulte [Início rápido sobre pesquisa de vídeo do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).