---
title: Atualizar API de Pesquisa de Imagem do Bing v5 para v7 | Microsoft Docs
description: Identifica as partes do aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363500"
---
# <a name="image-search-api-upgrade-guide"></a>Guia de atualização de API de Pesquisa de Imagem

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Imagem do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de última hora

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade é alterado de v5 para v7. Por exemplo, https://api.cognitive.microsoft.com/bing/\ * \*v7.0\*\*/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Adicionados os seguintes campos ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em segmentos discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`
   

- Substituídos os códigos de erro v5 com os seguintes valores `code` e `subCode` possíveis.

|Código|SubCode|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retorna ServerError sempre que ocorrer qualquer uma das condições de subcódigo. A resposta inclui esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retorna InvalidRequest sempre que uma parte da solicitação não é válida. Por exemplo, um parâmetro necessário está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro é ParameterMissing ou ParameterInvalidValue, o código de status HTTP é 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP é 410.
|RateLimitExceeded||O Bing retorna RateLimitExceeded sempre que você excede a cota de QPS (consultas por segundo) ou QPM (consultas por mês).<br/><br/>O Bing retorna o código de status HTTP 429 se QPS foi excedido e 403 se QPM foi excedido.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>Ocorre uma redundância se você especifica mais de um método de autenticação.<br/><br/>Se o erro é InvalidAuthorization, o código de status HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP é 403.

- O seguinte mapeia os códigos de erro anteriores para os novos códigos. Se você tiver obtido uma dependência em códigos de erro v5, atualize seu código adequadamente.

|Código da versão 5|Versão 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Desabilitado|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parâmetros de consulta

- Renomeado o parâmetro de consulta `modulesRequested` para [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Renomeadas as Anotações para Marcações. Consulte o parâmetro de consulta [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) para Marcações.  

- Alterada a lista de mercados com suporte do valor do filtro ShoppingSources para apenas en-US. Consulte [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Alterações de insights de imagem
  
- Renomeado o campo `annotations` de [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) para `imageTags`.  
  
- Renomeado o objeto `AnnotationModule` para [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Renomeado o objeto `Annotation` para [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag) e renomeado o campo `confidence`.  
  
- Renomeado o campo `insightsSourcesSummary` do objeto [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) para `insightsMetadata`.  
  
- Renomeado o objeto `InsightsSourcesSummary` para [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Adicionado o ponto de extremidade `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Use esse ponto de extremidade para solicitar insights de imagem em vez do ponto de extremidade /images/search. Consulte [Insights de Imagem](./image-insights.md). 
  
- Os parâmetros de consulta a seguir agora são válidos apenas com o ponto de extremidade `/images/details`.  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Renomeado o objeto `ImageInsightsResponse` para [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- Alterado os tipos de dados dos seguintes campos no objeto [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
    -   Alterado o tipo do campo `relatedCollections` de `ImageGallery[]` para [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Alterado o tipo do campo `pagesIncluding` de `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Alterado o tipo do campo `relatedSearches` de `Query[]` para [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Alterado o tipo do campo `recipes` de `Recipe[]` para [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Alterado o tipo do campo `visuallySimilarImages` de `Image[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Alterado o tipo do campo `visuallySimilarProducts` de `ProductSummaryImage[]` para [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Removido o objeto `ProductSummaryImage` e movido os campos relacionados ao produto para o objeto [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image). O objeto `Image` inclui os campos relacionados ao produto somente quando a imagem é incluída como parte de produtos visualmente similar em uma resposta de insight de imagem.  
  
    -   Alterado o tipo do campo `recognizedEntityGroups` de `RecognizedEntityGroup[]` para [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Renomeado o campo `categoryClassification` de [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) para `annotations` e alterado o tipo para `AnnotationsModule`.  

### <a name="images-answer"></a>Resposta de imagens

-   Removidos os campos displayShoppingSourcesBadges e displayRecipeSourcesBadges de [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  
  
-   Renomeado o campo `nextOffsetAddCount` de [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) para `nextOffset`. A maneira como o deslocamento é utilizado também alterou. Anteriormente, você definiu o parâmetro de consulta [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) para o valor `nextOffsetAddCount`, mais o valor de deslocamento anterior, mais o número de imagens no resultado. Agora, você define `offset` para o valor `nextOffset`.  
    
  
## <a name="non-breaking-changes"></a>Alterações contínuas

### <a name="query-parameters"></a>Parâmetros de consulta
  
- Adicionado Transparente como um possível valor do filtro [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype). O filtro Transparente retorna apenas imagens com um fundo transparente.

- Adicionado o valor do filtro Qualquer como uma possível [licença](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license). O filtro Qualquer retorna apenas imagens que estão sob licença.
  
- Adicionados os parâmetros de consulta [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize). Use esses filtros para retornar imagens dentro de um intervalo de tamanhos de arquivo.  
  
- Adicionados os parâmetros de consulta [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth). Use esses filtros para retornar imagens dentro de um intervalo de alturas e larguras.  

### <a name="object-changes"></a>Alterações de objeto
  
- Adicionados os campos `description` e `lastUpdated` para o objeto [Offer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer).  
  
- Adicionado o campo `name` para o objeto [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery).  
  
- Adicionado `similarTerms` para o objeto [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images). Este campo contém uma lista de termos que são semelhantes em significado à cadeia de caracteres de consulta do usuário.  
