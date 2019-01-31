---
title: Pesquisar vídeos mais populares na Web – Pesquisa de Vídeo do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como usar a API de Pesquisa de Vídeo do Bing para pesquisar vídeos populares na web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203548"
---
# <a name="get-trending-videos"></a>Obter vídeos populares  

Para obter vídeos populares atuais, envie a solicitação GET a seguir:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Os seguintes mercados oferecem suporte a vídeos populares.  
 
-   en-US (inglês, Austrália)  
-   en-CA (inglês, Canadá)  
-   en-GB (inglês, Grã-Bretanha)  
-   en-ID (inglês, Indonésia)  
-   en-IE (inglês, Irlanda)  
-   en-IN (inglês, Índia)  
-   en-NZ (inglês, Nova Zelândia)  
-   en-PH (inglês, Filipinas)  
-   en-SG (inglês, Singapura)  
-   en-US (inglês, Estados Unidos)  
-   en-WW (inglês, internacional agregar código)  
-   en-ZA (inglês, África do Sul)  
-   zh-CN (chinês, China)

  
O exemplo a seguir mostra uma resposta que contém vídeos populares.  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
A resposta contém uma lista de vídeos por categoria e subcategoria. Por exemplo, se a lista de categorias continha uma categoria de vídeos de música e uma de suas subcategorias era Principais, você pode criar uma categoria de principais vídeos de música em sua experiência de usuário. Você pode usar os campos `thumbnailUrl`, `displayText`, e `webSearchUrl` para criar um bloco clicável em cada categoria (por exemplo, principais vídeos de música). Quando o usuário clica no bloco, eles serão levados para o navegador de vídeo de Bing em que o vídeo é reproduzido.

A resposta também contém vídeos de faixa, que são os vídeos mais populares. Os vídeos de faixa podem vir de uma ou mais das categorias.  
  
