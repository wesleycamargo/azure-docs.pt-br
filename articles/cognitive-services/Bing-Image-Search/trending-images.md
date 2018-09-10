---
title: Pesquisar imagens populares na web | Microsoft Docs
description: Mostra como usar a API de Pesquisa de Imagem do Bing para pesquisar imagens populares na Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b12524cd4c1896501820209b3a45746b8f38b210
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363382"
---
# <a name="get-trending-images"></a>Obter imagens populares  

Para obter imagens populares de hoje, envie a solicitação GET a seguir:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A API de Imagens Populares atualmente suporta apenas os seguintes mercados:  

- en-US (inglês, Estados Unidos)  
- en-CA (inglês, Canadá)  
- en-US (inglês, Austrália)  
- zh-CN (chinês, China)

A resposta contém um objeto [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) que lista imagens por categoria. Use o `title` da categoria para agrupar as imagens na sua experiência de usuário. As categorias podem ser alteradas diariamente.  
  
```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  
  
Cada bloco contém uma imagem e opções para obter imagens relacionadas. Para obter as imagens relacionadas, você pode usar a consulta `text` para chamar a [API de Pesquisa de Imagem](./search-the-web.md) e exibir as imagens relacionadas por conta própria. Ou, você pode usar a URL no `webSearchUrl` para levar o usuário à página de resultados da pesquisa de imagens do Bing, que contém as imagens relacionadas. 

Se você chamar a API de Pesquisa de Imagem para obter as imagens relacionadas, defina o parâmetro de consulta [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) com a ID no campo `id`. Especificar a ID garante que a resposta contenha a imagem (é a primeira imagem na resposta) e suas imagens relacionadas. Além disso, defina o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) para o texto no `query` campo `text` do objeto.

O exemplo a seguir mostra como usar a ID de imagem para obter imagens relacionadas do Sr. Smith na resposta anterior da API de Imagens Populares.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
