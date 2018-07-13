---
title: Atualizar API de Pesquisa de Vídeo do Bing de v5 para v7 | Microsoft Docs
description: Identifica as partes do aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363437"
---
# <a name="video-search-api-upgrade-guide"></a>Guia de atualização da API de Pesquisa de Vídeo

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Vídeo do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de última hora

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade foi alterado de v5 para a v7. Por exemplo, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/videos/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Os campos a seguir foram adicionados ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`
   

- Os códigos de erro da v5 foram substituídos pelos possíveis valores `code` e `subCode` a seguir.

|Código|SubCode|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retorna ServerError sempre que ocorrer qualquer uma das condições de subcódigo. A resposta inclui esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retorna InvalidRequest sempre que uma parte da solicitação não é válida. Por exemplo, um parâmetro necessário está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP será 410.
|RateLimitExceeded||O Bing retorna RateLimitExceeded sempre que você excede a cota de QPS (consultas por segundo) ou QPM (consultas por mês).<br/><br/>O Bing retorna o código de status HTTP 429 se o QPS foi excedido e 403 se o QPM foi excedido.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>Ocorre uma redundância se você especifica mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP é 403.

- Os códigos a seguir mapeiam os códigos de erro anteriores para os novos códigos. Se você obteve uma dependência nos códigos de erro da v5, atualize o código de acordo.

|code da versão 5|code.subCode da versão 7
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

- Renomeado o parâmetro de consulta `modulesRequested` para [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Alterações de objeto

- Renomeado o campo `nextOffsetAddCount` de [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) para `nextOffset`. A maneira como o deslocamento é utilizado também alterou. Anteriormente, você definiu o parâmetro de consulta [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) para o valor `nextOffset`, mais o valor de deslocamento anterior, mais o número de vídeos no resultado. Agora, basta definir o parâmetro de consulta `offset` ao valor `nextOffset`.  
  
- Altere o tipo de dados do campo `relatedVideos` de `Video[]` para [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (consulte [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

