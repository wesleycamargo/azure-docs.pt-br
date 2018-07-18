---
title: Início rápido da API de Sugestão Automática | Microsoft Docs
description: Mostra como começar a usar a API de Sugestão Automática do Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364417"
---
# <a name="making-your-first-autosuggest-query"></a>Fazendo sua primeira consulta de Sugestão Automática

Antes de fazer a primeira chamada, você deverá obter uma chave de assinatura dos Serviços Cognitivos. Para obter uma chave, confira [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Para obter resultados de pesquisa na Web, envie uma solicitação GET para o seguinte ponto de extremidade:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Ponto de extremidade V7 versão prévia:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

A solicitação deve usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas em um servidor fornece um ponto único de upgrade para versões futuras da API.

A solicitação deve especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) que contém o termo de pesquisa parcial do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), que identifica o mercado de onde você deseja obter os resultados. Para uma lista de parâmetros de consulta opcionais, consulte [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta devem ser codificados em URL.

A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>A solicitação

A solicitação deve incluir todos os parâmetros e cabeçalhos de consulta sugeridos. Você chamaria essa API toda vez que o usuário digitasse um novo caractere na caixa de pesquisa. A integridade da cadeia de caracteres de consulta afeta a relevância dos termos de consulta sugeridos que a API retorna. Quanto mais completa a cadeia de caracteres de consulta, mais relevante é a lista de termos de consulta sugeridos. Por exemplo, as sugestões que a API pode retornar para *s*, provavelmente serão menos relevantes que as consultas que retorna para *barcos à vela*. 

O exemplo a seguir mostra uma solicitação que retorna as cadeias de caracteres de consulta sugeridas para *navegar*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Solicitação de V7 versão prévia:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Inclua apenas o cabeçalho da ID do cliente, se você já chamou uma API do Bing e o Bing retornou uma ID do cliente para a combinação de usuário e dispositivo.

O exemplo a seguir mostra a resposta à solicitação anterior. A resposta inclui um grupo de sugestões da Web que contém uma lista de sugestões de consulta de pesquisa. Cada sugestão inclui um campo `displayText`, `query` e `url`.

O campo `displayText` contém a consulta sugerida que você usaria para preencher a lista suspensa da caixa de pesquisa. Exiba todas as sugestões incluídas na resposta e na ordem determinada.  

Se o usuário selecionar uma consulta na lista suspensa, você poderá usar uma cadeia de caracteres de consulta no campo `query` para chamar a [API de Pesquisa do Bing](../bing-web-search/search-the-web.md) e exibir os resultados por conta própria. Ou, pode usar a URL no campo `url` para enviar o usuário à página de resultados da pesquisa do Bing.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Próximas etapas

Experimente a API. Acesse [Console de Teste de API de Sugestão Automática](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Para obter detalhes sobre como consumir os objetos de resposta, consulte [Obter termos de pesquisa sugeridos](./get-suggested-search-terms.md).
