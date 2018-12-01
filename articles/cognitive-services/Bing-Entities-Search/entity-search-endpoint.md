---
title: Pontos de extremidade da Pesquisa de Entidade do Bing
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade de API de Pesquisa de Entidade.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: aahi
ms.openlocfilehash: 578791c8beb34b08b28037977147d41db83f4a4d
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164907"
---
# <a name="entity-search-endpoints"></a>Pontos de extremidade de Pesquisa de Entidade
A **API de Pesquisa de Entidade**  inclui um ponto de extremidade.

## <a name="endpoint"></a>Ponto de extremidade
Para solicitar resultados de pesquisa de entidade, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

Ponto de extremidade `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Os parâmetros de URL a seguir são obrigatórios:
- mkt. O mercado de onde vêm os resultados. 
- q. A consulta de pesquisa de entidade.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Inícios rápidos da Pesquisa de Entidade do Bing](quickstarts/csharp.md)

## <a name="see-also"></a>Consulte também 

[Visão geral da Pesquisa de Entidade do Bing](search-the-web.md )
[Referência de API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
