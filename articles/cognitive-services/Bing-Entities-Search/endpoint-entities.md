---
title: Ponto de extremidade da Pesquisa de Entidade do Bing
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade de API de Pesquisa de Entidade do Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816575"
---
# <a name="entity-search-endpoint"></a>Ponto de extremidade de Pesquisa de Entidade
A **API de Pesquisa de Entidade** inclui um ponto de extremidade que retorna entidades da Web com base em uma consulta.

## <a name="endpoint"></a>Ponto de extremidade
Para obter os resultados da entidade usando a **API do Bing**, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade:** retorna entidades que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).

## <a name="response-json"></a>Resposta JSON
A resposta a uma solicitação de pesquisa de entidade inclui resultados como objetos JSON. Para exemplos dos resultados, consulte [Introdução](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para exemplos que usam a API de pesquisa de Entidade, consulte [Introdução](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) e [Redimensionamento e recorte de imagens em miniatura](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).