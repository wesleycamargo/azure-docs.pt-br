---
title: Ponto final da pesquisa na Web
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de Pesquisa na Web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2f7e6cd577b1eabbaabdfe87fca8ea0f036a062d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149383"
---
# <a name="web-search-endpoint"></a>Ponto de extremidade de Pesquisa na Web

A **API de Pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entidades têm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto de extremidade

Para obter os resultados de pesquisa na Web usando a API do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Os cabeçalhos e parâmetros de URL definem mais especificações.

**Ponto de extremidade**: retorna resultados Web que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto de extremidade: Para saber mais sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, confira a referência da [API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="response-json"></a>JSON de resposta

A resposta a uma solicitação de pesquisa na Web inclui todos resultados como objetos JSON. Analisar os resultados requer procedimentos que lidam com elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e [código-fonte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas

As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os endpoints suportam consultas que retornam um idioma e uma localização específicos por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa na Web, consulte [Início rápido sobre pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
