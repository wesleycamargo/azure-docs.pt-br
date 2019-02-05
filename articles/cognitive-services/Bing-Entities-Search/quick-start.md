---
title: 'Início Rápido: API de Pesquisa de Entidade do Bing'
description: Mostra como começar a usar a API de Pesquisa de Entidade do Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 1bb2c4b73b29d832a289c7a5c1c86b958302086a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153446"
---
# <a name="quickstart-making-your-first-bing-entity-search-request"></a>Início Rápido: Fazer sua primeira solicitação de Pesquisa de Entidade do Bing

A API de Pesquisa de Entidade do Bing envia uma consulta de pesquisa para o Bing e obtém os resultados que incluem entidades e locais. Os resultados de local incluem restaurantes, hotel ou outras empresas locais. Para locais, a consulta pode especificar o nome da empresa local ou ela pode solicitar uma lista (por exemplo, restaurantes próximos a mim). Os resultados de entidade incluem pessoas, lugares ou coisas. Local neste contexto é atrações turísticas, estados, países, etc. 

## <a name="first-steps"></a>Primeiras etapas

Antes de fazer a primeira chamada, você deverá obter uma chave de assinatura dos Serviços Cognitivos. Para obter uma chave, consulte [Experimentar Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Se a API de Pesquisa de Entidades não estiver visível na parte superior, clique na guia **Pesquisa** e role para baixo até você vê-la.)

## <a name="the-endpoint"></a>O ponto de extremidade

Para obter resultados de pesquisa de entidade e local, envie uma solicitação GET para o ponto de extremidade a seguir:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

A solicitação deve usar o protocolo HTTPS.

Recomendamos que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas a partir de um servidor fornece um único ponto de atualização para versões futuras da API.

## <a name="specifying-query-parameters-and-headers"></a>Especificar cabeçalhos e parâmetros de consulta

A solicitação deve especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), que contém o termo de pesquisa do usuário. A solicitação também deve especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), que identifica o mercado de onde você deseja que venham os resultados. Para uma lista de parâmetros de consulta opcionais, consulte [Parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). A URL codifica todos os parâmetros de consulta.  
  
A solicitação deve especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>A solicitação

O exemplo a seguir mostra uma solicitação de entidades que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID de cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

## <a name="the-response"></a>A resposta

O exemplo a seguir mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing. Para obter informações sobre o objeto de resposta, consulte [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>Próximas etapas

Experimente a API. Vá para [Console de teste de API de Pesquisa de Entidades](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Para obter detalhes sobre os objetos de resposta de consumo, consulte [Pesquisa na Web entidades e locais](./search-the-web.md).

Leia os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.
