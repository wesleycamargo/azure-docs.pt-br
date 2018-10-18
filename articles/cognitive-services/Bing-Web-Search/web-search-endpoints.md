---
title: Ponto final da pesquisa na Web
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de Pesquisa na Web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 3058ca6cf0eb99486dd4c269d43b274fb367f7a9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125452"
---
# <a name="web-search-endpoint"></a>Ponto de extremidade de Pesquisa na Web
A **API de Pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades contêm informações de resumo sobre uma pessoa, lugar ou tópico.
## <a name="endpoint"></a>Ponto de extremidade
Para obter os resultados de pesquisa na Web usando a API do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Os cabeçalhos e parâmetros de URL definem mais especificações.

**Ponto de extremidade:** retorna resultados Web que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Ponto de extremidade: Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="response-json"></a>Resposta JSON
A resposta a uma solicitação de pesquisa na Web inclui todos resultados como objetos JSON. Analisar os resultados requer procedimentos que lidam com elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa na Web, consulte [Início rápido sobre pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
