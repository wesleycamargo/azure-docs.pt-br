---
title: 'Pesquisa Personalizada do Bing: navegar pelas páginas da Web disponíveis | Microsoft Docs'
description: Mostra como navegar por todas as páginas da Web que o Bing pode retornar.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363361"
---
# <a name="paging-webpages"></a>Paginação de páginas da Web 

Quando você chama a API de Pesquisa Personalizada, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) do objeto de resposta.  
  
O exemplo a seguir mostra o campo `totalEstimatedMatches` que uma resposta da Web inclui.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Para navegar pelas páginas da Web disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset).  
  
O parâmetro `count` especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 50. O padrão é 10. O número real entregue pode ser menor que o solicitado.

O parâmetro `offset` especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  
  
Se você quiser exibir 15 páginas da Web por página, você definiria `count` como 15 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 15 (por exemplo, 15, 30).  
  
O exemplo a seguir mostra um exemplo que solicita 15 imagens começando no deslocamento 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Se o valor padrão `count` funcionar para a implementação, será necessário somente especificar o parâmetro de consulta `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

