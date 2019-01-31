---
title: Como percorrer os vídeos disponíveis – Pesquisa de Vídeo do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como paginar todos os vídeos que o Bing pode retornar.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181785"
---
# <a name="paging-videos"></a>Paginação de vídeos

Quando você chama a API de Pesquisa de Vídeo, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) do objeto de resposta.  
  
A exemplo a seguir mostra o campo `totalEstimatedMatches` que inclui uma resposta de vídeo.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Para paginar os vídeos disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset).  
  
O parâmetro `count` especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 105. O padrão é 35. O número real entregue pode ser menor que o solicitado.

O parâmetro `offset` especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  
  
Se você quiser exibir 20 vídeos por página, você definiria `count` como 20 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 20 (por exemplo, 20, 40).  

O exemplo a seguir mostra um exemplo que solicita 20 vídeos começando no deslocamento 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se o valor `count` padrão funciona para sua implementação, você precisa apenas especificar o parâmetro `offset` de consulta.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Normalmente, se você 35 vídeos de página por vez, você deve definir o parâmetro `offset` como 0 na sua primeira solicitação de consulta e, em seguida, incrementa `offset` por 35 em cada solicitação subsequente. No entanto, alguns dos resultados na resposta subsequente podem ser duplicados da resposta anterior. Por exemplo, os primeiros dois vídeos na resposta podem ser o mesmo que os últimos dois vídeos da resposta anterior.

Para eliminar resultados duplicados, use o campo [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) do objeto `Videos`.

Por exemplo, se você quiser paginar 30 vídeos por vez, você definiria `count` como 30 e `offset` como 0 na primeira solicitação. Na próxima solicitação, defina o parâmetro de consulta `offset` como valor `nextOffset`.


> [!NOTE]
> Paginação aplica-se somente a pesquisa de vídeos (vídeos/pesquisa) e não a insights de vídeo (vídeos/detalhes) ou tendências vídeos (vídeos/tendência).

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, você pode recuperar da consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 
