---
title: Atualizar API de Verificação Ortográfica do Bing v5 para v7 | Microsoft Docs
description: Identifica as partes do aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7DC8FB29-4732-47D8-824B-CF2D7AEBA07B
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 1b0406641053fac8a4b3f4721728ad3b6c313ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363375"
---
# <a name="spell-check-api-upgrade-guide"></a>Guia de atualização de API de Verificação Ortográfica

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Verificação Ortográfica do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de última hora

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade é alterado de v5 para v7. Por exemplo, https://api.cognitive.microsoft.com/bing/\*\*v7.0 **/corretor ortográfico.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Adicionados os seguintes campos ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em segmentos discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`
   

- Substituídos os códigos de erro v5 com os seguintes valores `code` e `subCode` possíveis.  
  
|Código|Subcódigo|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing retorna ServerError sempre que ocorrer qualquer uma das condições de subcódigo. A resposta inclui esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|Bing retorna InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP é 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP é 410.
|RateLimitExceeded||Bing retorna RateLimitExceeded sempre que excederem sua consultas por segundo (QPS) ou a cota de consultas por mês (QPM).<br/><br/>Bing retorna o código de status HTTP 429 se QPS foi excedido e 403 se QPM foi excedido.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retorna InvalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>Ocorre redundância se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP é 403.

- O seguinte mapeia os códigos de erro anteriores para os novos códigos. Se você tiver obtido uma dependência em códigos de erro v5, atualize seu código adequadamente.  
  
|Código da versão 5|Versão 7 code.subCode
|-|-
|RequestParameterMissing|RequestParameterMissing
RequestParameterInvalidValue|RequestParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Desabilitado|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.UnexpectedError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|RequestParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Requisitos de uso e exibição](./UseAndDisplayRequirements.md)
