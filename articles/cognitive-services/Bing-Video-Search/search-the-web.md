---
title: O que é a Pesquisa de Vídeo do Bing?
titlesuffix: Azure Cognitive Services
description: Mostra como usar a API de Pesquisa de Vídeo do Bing para pesquisar vídeos na Web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: cf37db9bffa8b2a54a6327c29ec806e0eefc8c91
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225405"
---
# <a name="what-is-bing-video-search"></a>O que é a Pesquisa de Vídeo do Bing?

A API de Pesquisa de Vídeo do Bing fornece uma experiência semelhante (mas não igual) à do [Bing Vídeos](https://www.bing.com/videos). A API de Pesquisa de Vídeo do Bing permite enviar uma consulta de pesquisa ao Bing e obter uma lista de vídeos relevantes.

Se estiver compilando uma página de resultados da pesquisa somente de vídeos para encontrar vídeos que sejam relevantes para a consulta de pesquisa do usuário, chame essa API em vez de chamar a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md) mais geral. Se você quiser vídeos e outros tipos de conteúdo, como páginas da Web, vídeos e imagens, chame a API de Pesquisa na Web do Bing.

## <a name="suggesting--using-search-terms"></a>Sugerir e usar termos de pesquisa

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere seu termo de pesquisa, a URL codifica-o antes de definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="getting-videos"></a>Como obter vídeos

Para obter vídeos relacionados ao termo de pesquisa do usuário da Web, envie a solicitação GET a seguir:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Todas as solicitações devem ser feitas a partir de um servidor.

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

Para obter vídeos de um domínio específico, use o operador de consulta [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta de [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) contendo uma lista de vídeos que o Bing considerou serem relevantes para a consulta. Cada objeto [Vídeo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) na lista inclui a URL do vídeo, sua duração, suas dimensões e seu formato de codificação, entre outros atributos. O objeto de vídeo também inclui a URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

Você poderia exibir uma colagem de todas as miniaturas de vídeo ou exibir um subconjunto das miniaturas. Se você exibir um subconjunto, forneça ao usuário a opção para exibir os vídeos restantes. Você deve exibir os vídeos na ordem fornecida na resposta. Para obter informações sobre o redimensionamento da miniatura, consulte [Redimensionando e cortando miniaturas](./resize-and-crop-thumbnails.md). 

À medida que o usuário passa o mouse sobre a miniatura, é possível usar [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) para reproduzir uma versão em miniatura do vídeo. Atribua a miniatura de movimento ao exibi-la.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Se o usuário clicar na miniatura, estas serão as opções de exibição de vídeo:

- Use [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) para exibir o vídeo no site do host (por exemplo, no YouTube)
- Use [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) para exibi o vídeo no navegador de vídeo do Bing
- Use [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) para inserir o vídeo em sua própria experiência 

Use o editor e o criador para o atribuir o vídeo durante a reprodução.

Para obter detalhes sobre como usar [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) para obter informações sobre o vídeo, veja [Insights de vídeo](./video-insights.md).

## <a name="filtering-videos"></a>Como filtrar vídeos

Por padrão, a API de pesquisa de vídeo retorna todos os vídeos relevantes para a consulta. Se você só quiser vídeos gratuitos ou vídeos de menos de cinco minutos de duração, use os seguintes parâmetros de consulta de filtro:

- [preços](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;Filtrar vídeos por preço (por exemplo, vídeos gratuitos ou pelos quais você precise pagar)
- [resolução](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;Filtrar vídeos por resolução (por exemplo, vídeos com uma resolução de 720p ou mais alta)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;Filtrar vídeos por duração do vídeo (por exemplo, vídeos de menos de cinco minutos de duração)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;Filtrar vídeos por idade (por exemplo, vídeos descobertos pelo Bing na última semana)

Para obter os vídeos de um domínio específico, inclua o operador de consulta [site:](http://msdn.microsoft.com/library/ff795613.aspx) na cadeia de caracteres da consulta.

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a possibilidade de que a resposta contenha conteúdo para adulto, independentemente da configuração [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch). Só use `site:` se estiver ciente sobre o conteúdo do site e se seu cenário permitir a possibilidade de obtenção de conteúdo adulto.

O exemplo a seguir mostra como obter vídeos gratuitos de ContosoSailing.com que tenham uma resolução de 720p ou melhor e que o Bing tenha descoberto no mês passado.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandindo a consulta

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) conterá o campo `queryExpansions`. Por exemplo, se a consulta for *Limpeza de Sarjetas*, as consultas expandidas poderão ser: **Ferramentas** de Limpeza de Sarjetas, Limpeza de Sarjetas **do Solo**, **Máquina** de Limpeza de Sarjetas e Limpeza de Sarjetas **fácil**.

O exemplo a seguir mostra as consultas expandidas para *Limpeza de Sarjetas*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj). O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. É possível usar os campos texto e miniatura para exibir as cadeias de caracteres da consulta expandida para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="pivoting-the-query"></a>Dinamizando a consulta

Se o Bing conseguir segmentar a consulta de pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) contém o campo `pivotSuggestions`. Por exemplo, se a consulta original for *Limpeza de Sarjetas*, o Bing poderá segmentar a consulta em *Limpeza* e *Sarjetas*.

O seguinte exemplo mostra as sugestões de pivô para *Limpeza de Sarjetas*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Para cada tipo de dinâmica, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) que contem consultas sugeridas. O campo `text` contém a consulta sugerida, e o campo `displayText` contém o termo que substitui a dinâmica na consulta original. Por exemplo, Limpeza de Janela.

É possível usar os campos `text` e `thumbnail` para exibir as cadeias de caracteres da consulta expandida para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira [Fazendo sua primeira solicitação](./quick-start.md).

Para obter sua chave de assinatura, veja [Chaves de Assinatura](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Familiarize-se com a referência [API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os resultados da pesquisa. Ela também inclui as definições dos objetos de resposta. 

Para melhorar sua experiência de usuário com a caixa de pesquisa, consulte [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md). Como o usuário insere seu termo de consulta, você pode chamar essa API para obter os termos de consulta relevantes que foram usados por outras pessoas.

Leia os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.

Quando você chama a API de Pesquisa de Vídeo, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de vídeos disponíveis para exibição. Para obter detalhes sobre como percorrer os vídeos restantes, veja [Paginação de vídeos](./paging-videos.md).

Para obter detalhes sobre como obter informações sobre um vídeo, veja [Insights de vídeo](./video-insights.md).

Para obter detalhes sobre como obter vídeos mais populares, veja [Vídeos mais populares](./trending-videos.md).