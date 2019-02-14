---
title: 'Início Rápido: API de Verificação Ortográfica do Bing'
titlesuffix: Azure Cognitive Services
description: Mostra como começar a usar a API de Verificação Ortográfica do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 70aec949dd8f1b27a2a04c28a892b4caa2122844
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867810"
---
# <a name="quickstart-your-first-spell-check-request"></a>Início Rápido: Sua primeira solicitação de verificação ortográfica

Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Para verificar se uma cadeia de caracteres de texto possui erros de gramática e ortografia, você poderia enviar uma solicitação GET para o ponto de extremidade a seguir:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Ponto de extremidade V7 versão prévia:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
A solicitação deve usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, chamadas a partir de um servidor fornecem um único ponto de atualização para versões futuras da API.

A solicitação deve especificar o parâmetro de consulta [texto](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text), que contém a cadeia de caracteres de texto para verificação. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt), que identifica o mercado de onde você deseja que venham os resultados. Para uma lista de parâmetros de consulta opcionais, como `mode`, consulte [Parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta precisam ser codificados em URL.  
  
A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, consulte [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>A solicitação

O exemplo a seguir mostra uma solicitação que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID de cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Solicitação de v7 versão prévia:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

O exemplo a seguir mostra a resposta para a solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>Próximas etapas

Experimente a API. Vá para [Console de teste da API de Verificação Ortográfica](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Para obter detalhes sobre os objetos de resposta de consumo, consulte [Cadeias de caracteres de texto de verificação ortográfica](./proof-text.md).

