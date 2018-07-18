---
title: O que é a Pesquisa de Imagem do Bing? | Microsoft Docs
description: Saiba como usar a API de Pesquisa de Imagem do Bing para pesquisar imagens na Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336512"
---
# <a name="what-is-bing-image-search"></a>O que é a Pesquisa de Imagem do Bing?

A API de Pesquisa de Imagem do Bing proporciona uma experiência semelhante ao [Bing Imagens](https://www.bing.com/images), permitindo que você envie uma consulta de pesquisa de usuário ao Bing e obtenha uma lista de imagens relevantes.

Se estiver compilando uma página de resultados de pesquisa somente imagens para encontrar imagens que sejam relevantes para a consulta de pesquisa do usuário, chame essa API em vez de chamar a [API de Pesquisa da Web](../bing-web-search/search-the-web.md) mais geral. Se você quiser imagens e outros tipos de conteúdo, como páginas da Web, notícias e vídeos, chame a API de Pesquisa na Web.

## <a name="suggesting--using-search-terms"></a>Sugerir e usar termos de pesquisa

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere seu termo de pesquisa, a URL codifica o termo antes de definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="getting-images"></a>Obtendo imagens

Para obter imagens relacionadas ao termo de pesquisa do usuário da Web, envie a solicitação GET a seguir:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Todas as solicitações devem ser feitas a partir de um servidor. Você não pode fazer chamadas a partir de um cliente.

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID de cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

Para obter imagens de um domínio específico, use o operador de consulta [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta de [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contendo uma lista de imagens que o Bing considerou serem relevantes para a consulta. Cada objeto de [Imagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) na lista inclui a URL da imagem, seu tamanho, suas dimensões e seu formato de codificação. O objeto de imagem também inclui a URL de uma miniatura da imagem e as dimensões da miniatura.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Você poderia exibir uma colagem de todas as miniaturas de imagem ou exibir um subconjunto das miniaturas. Se você exibir um subconjunto, forneça ao usuário a opção para exibir as imagens restantes. Você deve exibir as imagens na ordem fornecida na resposta.

Também é possível expandir a miniatura quando o usuário passar o cursor sobre ela. Se expandir a imagem, verifique se ela foi atribuída. Por exemplo, extraindo o host de [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) e exibindo-o abaixo da imagem. Para obter informações sobre o redimensionamento da miniatura, consulte [Redimensionando e cortando miniaturas](./resize-and-crop-thumbnails.md).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o usuário clicar na miniatura, você pode usar [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) para exibir a imagem em tamanho máximo para o usuário. Verifique se a imagem foi atribuída.

Se `shoppingSourcesCount` ou `recipeSourcesCount` forem maiores que zero, adicione selos, como um carrinho de compras, para a miniatura indicar que existem compras ou receitas para o item na imagem.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Para obter informações sobre a imagem, como páginas da Web que incluam a imagem ou pessoas que foram reconhecidas na imagem, use [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Para obter detalhes, consulte [Insights de imagem](./image-insights.md).

## <a name="filtering-images"></a>Filtrando imagens

 Por padrão, a API de Pesquisa de Imagem retorna todas as imagens que são relevantes para a consulta. Mas se você desejar apenas imagens com um plano de fundo transparente ou imagens de um tamanho específico, deve usar seguintes parâmetros de consulta para filtrar as imagens que o Bing retorna.  

- [aspecto](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)— Filtrar imagens por taxa de proporção (por exemplo, imagens de tela grande ou padrão)
- [cor](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)— Filtrar imagens por cor dominante ou preto e branco
- [atualização](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)— Filtrar imagens por idade (por exemplo, imagens descobertas pelo Bing na última semana)
- [altura](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [largura](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)— Filtrar imagens por largura e altura
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)— Filtrar imagens por conteúdo (por exemplo, imagens que mostram apenas o rosto de uma pessoa)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)— Filtrar imagens por tipo (por exemplo, clip-art, GIFs animados ou planos de fundo transparentes)
- [licença](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)— Filtrar imagens pelo tipo de licença associado ao site
- [tamanho](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)— Filtrar imagens por tamanho, como imagens pequenas de até 200x200 pixels

Para obter imagens de um domínio específico, use o operador de consulta [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> Dependendo da consulta, se você usar o operador `site:`, há a possibilidade de que a resposta contenha conteúdo adulto, independentemente da configuração [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Só use `site:` se estiver ciente sobre o conteúdo do site e se seu cenário oferece suporte para a possibilidade de conteúdo adulto.

O exemplo a seguir mostra como obter imagens pequenas de ContosoSailing.com que o Bing descobriu na última semana.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Dinamizando a consulta

Se o Bing conseguir segmentar a consulta de pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contém o campo `pivotSuggestions`. Por exemplo, se a consulta original foi *Microsoft Surface*, o Bing pode segmentar a consulta entre *Microsoft* e *Surface*.  

O exemplo a seguir mostra as sugestões de dinâmica do *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

É possível exibir ao usuário os termos da consulta opcional caso seja do interesse do usuário.

O campo `pivotSuggestions` contém a lista de segmentos (dinâmicas) em que a consulta original foi dividida. Para cada tipo de dinâmica, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) que contem consultas sugeridas. O campo `text` contém a consulta sugerida, e o campo `displayText` contém o termo que substitui a dinâmica na consulta original. Por exemplo, data de lançamento do Surface.

Você pode usar os campos `text` e `thumbnail` para exibir as cadeias de caracteres da consulta dinâmica para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Expandindo a consulta

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contém o campo `queryExpansions`. Por exemplo, se a consulta foi *Microsoft Surface*, as consultas expandidas podem ser: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** e Microsoft Surface **Hub**.

O exemplo a seguir mostra as consultas expandidas do *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. É possível usar os campos `text` e `thumbnail` para exibir as cadeias de caracteres da consulta expandida para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Para uma introdução rápida à primeira solicitação, consulte [o guia de início rápido de C#](quickstarts/csharp.md).

Familiarize-se com a referência [API de Pesquisa de Imagem do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os resultados da pesquisa. Ela também inclui as definições dos objetos de resposta.

Para melhorar sua experiência de usuário com a caixa de pesquisa, consulte [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md). Como o usuário insere seu termo de consulta, você pode chamar essa API para obter os termos de consulta relevantes que foram usados por outras pessoas.

Leia os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.

Quando você chamar a API de Pesquisa de Imagem do Bing, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de imagens disponíveis para exibição. Para obter detalhes sobre como percorrer as imagens restantes, consulte [Paginação de imagens](./paging-images.md).