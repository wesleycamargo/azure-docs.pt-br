---
title: O que é a Sugestão Automática do Bing? | Microsoft Docs
description: Saiba como usar a API de Sugestão Automática do Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 6F4AFEDA-71A7-48C1-B3E2-D0D430428CDC
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 76e509289f495e09c367af926fd26e0581b6e7c6
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091099"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

Se enviar consultas para qualquer uma das APIs de Pesquisa do Bing, você poderá usar a API de Sugestão Automática do Bing para aprimorar sua experiência de caixa de pesquisa. A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial inserida pelo usuário na caixa de pesquisa. Exiba as sugestões na lista suspensa da caixa de pesquisa. Os termos sugeridos são baseados em consultas sugeridas que outros usuários pesquisaram e na intenção do usuário.

Normalmente, você chamaria essa API toda vez que o usuário digitasse um novo caractere na caixa de pesquisa. A integridade da cadeia de caracteres de consulta afeta a relevância dos termos de consulta sugeridos que a API retorna. Quanto mais completa a cadeia de caracteres de consulta, mais relevante é a lista de termos de consulta sugeridos. Por exemplo, as sugestões que a API pode retornar para *s*, provavelmente serão menos relevantes que as consultas que retorna para *barcos à vela*.

## <a name="getting-suggested-search-terms"></a>Obtendo termos de pesquisa sugeridos

O exemplo a seguir mostra uma solicitação que retorna as cadeias de caracteres de consulta sugeridas para *navegar*. Lembre-se de codificar a URL do termo de consulta parcial do usuário quando você definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing les*, defina `q` como `sailing+les` ou `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A resposta a seguir contém uma lista de objetos de [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) que contêm os termos de consulta sugeridos.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Cada sugestão inclui um campo `displayText`, `query` e `url`. O campo `displayText` contém a consulta sugerida que você usa para preencher a lista suspensa de sua caixa de pesquisa. Exiba todas as sugestões incluídas na resposta e na ordem determinada.

O exemplo a seguir mostra um exemplo de caixa de pesquisa com lista suspensa com os termos de consulta sugeridos.

![Lista suspensa da caixa de pesquisa com sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se o usuário selecionar uma consulta sugerida na lista suspensa, você poderá usar o termo de consulta no campo `query` para chamar a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md) e exibir os resultados por conta própria. Ou você pode usar a URL no campo `url` para enviar o usuário à página de resultados da pesquisa do Bing.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira [Fazendo sua primeira consulta](quickstarts/csharp.md).

Familiarize-se com a referência [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os termos de consulta sugeridos, além das definições dos objetos de resposta.

Saiba como pesquisar na Web usando a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md).

Leia os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.