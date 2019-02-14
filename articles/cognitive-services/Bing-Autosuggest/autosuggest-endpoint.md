---
title: Ponto de extremidade da Sugestão Automática
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de Sugestão Automática do Bing.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 9e80e81a79f45f132baf07af7c2e7be8f98ceec4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857542"
---
# <a name="bing-autosuggest-endpoint"></a>Ponto de extremidade da Sugestão Automática

A **API de Sugestão Automática do Bing** inclui um ponto de extremidade, que retorna uma lista de consultas sugeridas com base em um termo de pesquisa parcial.

## <a name="endpoint"></a>Ponto de extremidade

Para obter as consultas sugeridas usando a API do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade:** Retorna as sugestões de pesquisa como resultados JSON que são relevantes à entrada do usuário definida por `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., confira a referência [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference).

## <a name="response-json"></a>JSON de resposta

A resposta a uma solicitação de sugestão automática inclui os resultados como objetos JSON. Para obter exemplos de como analisar os resultados, confira o [tutorial](tutorials/autosuggest.md) e o [código-fonte](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Próximas etapas

As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.
Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Sugestão Automática, confira [Inícios Rápidos da Sugestão Automática](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
