---
title: Enviando consultas para a API de Pesquisa de Imagem do Bing | Microsoft Docs
description: Saiba mais sobre como enviar e personalizar as consultas de pesquisa enviadas para a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082569"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Enviando consultas para a API de Pesquisa de Imagem do Bing

A API de Pesquisa de Imagem do Bing proporciona uma experiência semelhante a Bing.com/Images, permitindo que você envie uma consulta de pesquisa de usuário ao Bing e obtenha uma lista de imagens relevantes.

## <a name="using-and-suggesting-search-terms"></a>Usando e sugerindo termos de pesquisa

Depois de um termo de pesquisa ser inserido, codifique em URL o termo antes de definir o parâmetro de consulta [**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Por exemplo, se um usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

Se o aplicativo tiver uma caixa de pesquisa em que os termos de pesquisa são inseridos, use a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência exibindo os termos de pesquisa sugeridos em tempo real. A API retorna cadeias de consulta sugeridas com base em termos de pesquisa parciais e nos serviços cognitivos do Azure.

## <a name="pivoting-the-query"></a>Dinamizando a consulta

Se o Bing puder segmentar a consulta de pesquisa original, o objeto [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) retornado conterá `pivotSuggestions`, que pode ser exibido como termos de pesquisa opcionais para o usuário. Por exemplo, se a consulta original foi *Microsoft Surface*, o Bing pode segmentar a consulta entre *Microsoft* e *Surface* e fornecer pivôs sugeridos para cada um. Eles podem ser exibidos como termos de consulta opcionais para o usuário.

O seguinte exemplo mostra as sugestões de pivô para o *Microsoft Surface*:  

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Se você ainda não experimentou a API de Pesquisa de Imagem do Bing, experimente seguir um [início rápido](../quickstarts/csharp.md). Se estiver procurando algo mais complexo, experimente seguir o tutorial para a criação de um [aplicativo Web de página única](../tutorial-bing-image-search-single-page-app.md).
