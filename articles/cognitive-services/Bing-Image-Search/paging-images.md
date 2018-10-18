---
title: Como navegar pelas imagens disponíveis - API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como navegar por todas as imagens que o Bing pode retornar.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 019d91f6a86bab5c4f446085e0244f9b5323f1fb
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294400"
---
# <a name="paging-results"></a>Resultados da paginação

Quando você chama a API de Pesquisa de Imagem, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) do objeto de resposta.  

A exemplo a seguir mostra o campo `totalEstimatedMatches` que inclui uma resposta de Imagens.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

Para navegar pelas imagens disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset).  

O parâmetro `count` especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 150. O padrão é 35. O número real entregue pode ser menor que o solicitado.

O parâmetro `offset` especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  

Se você quiser exibir 20 imagens por página, defina `count` como 20 e `offset` como 0 para obter a primeira página de resultados. O exemplo a seguir mostra um exemplo que solicita 20 imagens começando no deslocamento 40.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se o valor padrão `count` funcionar para a implementação, será necessário somente especificar o parâmetro de consulta `offset`.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Normalmente, se você navegasse por 35 imagens por vez, definiria o parâmetro de consulta `offset` para 0 na primeira solicitação e, em seguida, incrementaria `offset` por 35 em cada solicitação subsequente. No entanto, alguns dos resultados na resposta subsequente podem ser duplicados da resposta anterior. Por exemplo, as duas primeiras imagens na resposta podem ser as mesmas das duas últimas imagens da resposta anterior.

Para eliminar resultados duplicados, use o campo [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) do objeto `Images`. O campo `nextOffset` informa `offset` para usar na próxima solicitação. Por exemplo, se quisesse navegar 30 imagens por vez, definiria `count` para 30 e `offset` para 0 na primeira solicitação. Na próxima solicitação, você definiria `count` para 30 e `offset` para o valor da resposta anterior `nextOffset`. Para paginar no sentido contrário, é recomendável manter uma pilha de deslocamentos anteriores e retirar o mais recente.

> [!NOTE]
> A paginação aplica-se apenas à pesquisa de imagens (/images/search) e não a insights de imagens ou imagens de tendência (/images/trending).
