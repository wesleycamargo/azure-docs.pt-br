---
title: Autenticar com o Azure AD e obter um token JWT usando o OAuth 2.0
description: Exemplo de código mostrando como autenticar com o Active Directory do Azure usando o OAuth 2.0 para acessar aplicativos da web seguros e APIs da web em sua organização.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: d77af898d5baef4fa7970132b0eb8deddb8f68cb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981790"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Solicitar um token de acesso usando o OAuth 2.0 para acessar APIs da web e aplicativos protegidos pelo Active Directory do Azure

Este artigo mostra como obter um JSON Web Token (JWT) para acessar recursos protegidos pelo Azure AD. Ele supõe que você tenha um [token de autorização](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) da permissão concedida pelo usuário ou por meio de um [principal de serviço](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>Criar a solicitação

Use a seguinte solicitação HTTP `POST` para que um JWT acesse um aplicativo ou API específico protegido pelo Azure AD.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Tipo de Conteúdo:*| Application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>Parâmetros de URI

| Parâmetro     |                       | DESCRIÇÃO                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| locatário        | obrigatório              | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | obrigatório              | A ID do Aplicativo que o Portal de Registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuiu ao aplicativo.                                                                                                                                                                                                                             |
| grant_type    | obrigatório              | Deve ser `authorization_code` para o fluxo do código de autorização.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | obrigatório              | Uma lista de escopos separados por espaços. Os escopos solicitados nessa etapa devem ser equivalentes ou um subconjunto dos escopos da chamada para `/authorize`. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade v2.0 retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| código          | obrigatório              | O authorization_code que você adquiriu na chamada para `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | obrigatório              | O mesmo valor de redirect_uri que foi usado para adquirir o authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Não use em um aplicativo nativo, porque client_secrets não podem ser armazenados de forma confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client_secret com segurança no servidor.  Segredos de clientes devem ser codificados por URL antes de serem enviados.                                                                                 |
| code_verifier | opcional              | O mesmo code_verifier que foi usado para obter o authorization_code. Obrigatório se o PKCE foi usado na solicitação de concessão de código de autorização. Para obter mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>Tratar da resposta

Uma resposta de token bem-sucedida conterá um token JWT e terá a seguinte aparência:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro     | DESCRIÇÃO                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | O [token de acesso](access-tokens.md) solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Por quanto tempo o token de acesso é válido (em segundos).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | Os escopos para os quais o access_token é válido.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os Refresh_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, confira a [referência à concessão de código v2.0](v2-oauth2-auth-code-flow.md#refresh-the-access-token). <br> **Observação:** somente fornecido se o escopo `offline_access` for solicitado.                                               |
| id_token      | Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |



