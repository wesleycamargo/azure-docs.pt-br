---
title: Solicitar um token de acesso - Azure Active Directory B2C | Microsoft Docs
description: Saiba como solicitar um token de acesso do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5670d8b3c97cc1f9f6d149e8eadaa60d527e45f5
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683929"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Solicitar um token de acesso no Azure Active Directory B2C

Uma *token de acesso* contém declarações que você pode usar no Azure Active Directory (Azure AD) B2C para identificar as permissões concedidas às suas APIs. Ao chamar um servidor de recursos, um token de acesso deve estar presente na solicitação HTTP. Um token de acesso é denotado como **access_token** nas respostas do Azure AD B2C. 

Este artigo mostra como solicitar um token de acesso para um aplicativo web e a API da web. Para obter mais informações sobre tokens no Azure AD B2C, consulte o [visão geral dos tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Não há suporte a cadeias de API Web (em nome de) no Azure AD B2C.** -Muitas arquiteturas incluem uma API web que precisa chamar outra API web downstream, ambas protegidas pelo Azure AD B2C. Esse cenário é comum em clientes que têm um API da web back-end, que por sua vez chama um outro serviço. Este cenário de API Web encadeada pode ter suporte usando a concessão de Credencial de Portador JWT do OAuth 2.0, também conhecida como fluxo Em nome de. No entanto, o fluxo Em Nome de não está implementado atualmente no Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.
- Se você ainda não fez isso, [adicionar um aplicativo de API para seu locatário do Azure Active Directory B2C da web](add-web-application.md).

## <a name="scopes"></a>Escopos

Os escopos fornecem uma maneira de gerenciar permissões para recursos protegidos. Quando um token de acesso é solicitado, o aplicativo cliente precisa especificar as permissões desejadas na **escopo** parâmetro da solicitação. Por exemplo, para especificar o **valor de escopo** dos `read` para a API que tem o **URI da ID do aplicativo** de `https://contoso.onmicrosoft.com/api`, o escopo seria `https://contoso.onmicrosoft.com/api/read`.

Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Para adquirir várias permissões na mesma solicitação, você pode adicionar várias entradas na única **escopo** parâmetro da solicitação, separado por espaços.

O exemplo a seguir mostra os escopos decodificados em uma URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

O exemplo a seguir mostra os escopos codificados em uma URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se você solicitar mais escopos do que as concedidas para seu aplicativo cliente, a chamada é bem-sucedida se pelo menos uma permissão for concedida. O **scp** declaração no token de acesso resultante é preenchida com apenas as permissões que foram concedidas com êxito. O padrão de OpenID Connect especifica vários valores especiais de escopo. Os seguintes escopos representam a permissão para acessar o perfil do usuário:

- **OpenID** -solicita um token de ID.
- **offline_access** -solicita um token de atualização usando [o código de autenticação flui](active-directory-b2c-reference-oauth-code.md).

Se o **response_type** parâmetro em um `/authorize` solicitação inclui `token`, o **escopo** parâmetro deve incluir o escopo de pelo menos um recurso diferente de `openid` e `offline_access`que será concedido. Caso contrário, o `/authorize` solicitação falhará.

## <a name="request-a-token"></a>Solicitar um token

Para solicitar um token de acesso, você precisa de um código de autorização. Abaixo está um exemplo de uma solicitação para o `/authorize` ponto de extremidade para um código de autorização. Não há suporte para domínios personalizados para uso com tokens de acesso. Use seu domínio de locatário name.onmicrosoft.com na URL da solicitação.

Substitua esses valores no exemplo a seguir:

- `<tenant-name>` – O nome de seu locatário do Azure AD B2C.
- `<policy-name>` – O nome da sua política personalizada ou o fluxo de usuário.
- `<application-ID>` -O identificador do aplicativo do aplicativo web que você registrou para dar suporte ao fluxo de usuário.
- `<redirect-uri>` – O **URI de redirecionamento** que você inseriu ao registrar o aplicativo cliente.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

A resposta com o código de autorização deve ser semelhante a este exemplo:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Depois de receber com êxito o código de autorização, você pode usá-lo para solicitar um token de acesso:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Você deverá ver algo semelhante à seguinte resposta:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Ao usar https://jwt.ms para examinar o token de acesso que foi retornado, você deverá ver algo semelhante ao exemplo a seguir:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [configurar tokens no Azure AD B2C](configure-tokens.md)
