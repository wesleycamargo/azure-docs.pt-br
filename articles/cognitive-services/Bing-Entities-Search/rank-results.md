---
title: Usar a classificação para exibir as respostas ‒ Pesquisa de Entidade do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como usar a classificação para exibir as respostas que retorna a API de Pesquisa de Entidade do Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 4a336ccaea18ab84464f28aef170ccdc423b216d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814586"
---
# <a name="using-ranking-to-display-results"></a>Usando a classificação para exibir os resultados  

Cada resposta da pesquisa de entidade inclui uma resposta [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), semelhante em uma resposta de Pesquisa na Web do Bing, que especifica como você deve exibir os resultados da pesquisa. A resposta de classificação agrupa os resultados no polo, principal e o conteúdo da barra lateral. O resultado de polo é o resultado mais importante ou proeminente e deve ser exibido primeiro. Se você não exibir os resultados restantes em um formato de barra lateral e principal tradicional, você deve fornecer a visibilidade de maior conteúdo principal que o conteúdo da barra lateral. 
  
Em cada grupo, a matriz de [Itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem na qual o conteúdo deve aparecer. Cada item fornece duas maneiras para identificar o resultado em uma resposta.  
  
-   `answerType` e `resultIndex` — O campo `answerType` identifica a resposta (entidade ou local) e `resultIndex` identifica um resultado dentro de resposta (por exemplo, uma entidade). O índice é baseado em zero.  
  
-   `value` — O campo `value` contém uma ID que corresponde à ID de uma resposta ou um resultado dentro de resposta. A resposta ou os resultados contêm a ID, mas não ambos.  
  
Usar a ID requer correspondência da ID de classificação com a ID de uma resposta ou um dos seus resultados. Se um objeto de resposta inclui um campo `id`, exiba todos os resultados da resposta juntos. Por exemplo, se o objeto `Entities` inclui o campo `id`, exibir todos os artigos de entidades juntos. Se o objeto `Entities` não inclui o campo `id`, em seguida, cada entidade contém um campo `id` e a resposta de classificação mescla as entidades com os resultados de locais.  
  
Usar o `answerType` e `resultIndex` é um processo de duas etapas. Primeiro, você usa `answerType` para identificar a resposta que contém os resultados para exibir. Em seguida, use `resultIndex` para indexar em resultados da resposta para obter o resultado para exibir. (O valor `answerType` é o nome do campo no objeto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse).) Se você precisa para exibir todos os resultados da resposta juntos, a resposta de classificação de item não inclui o campo `resultIndex`.

## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

A seguir é mostrado um exemplo de [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Com base nessa resposta de classificação, a barra lateral exibirá os resultados de duas entidades relacionados ao Jimi Hendrix.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial da Pesquisa de Entidade do Bing](tutorial-bing-entities-search-single-page-app.md)
