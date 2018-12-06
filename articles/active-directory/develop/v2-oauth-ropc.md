---
title: Use o Azure AD v2.0 para entrar usuários usando o ROPC | Microsoft Docs
description: Suporta fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3beda2dc9214900a11bdd4422637f7dec3ce4924
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576906"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory v2.0 e a credencial de senha do proprietário do recurso OAuth 2.0

O Active Directory do Azure (Azure AD) oferece suporte à concessão de [credencial de senha do proprietário do recurso (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo entre no usuário manipulando diretamente sua senha. O fluxo de ROPC requer um alto grau de confiança e exposição do usuário, e os desenvolvedores só devem usar esse fluxo quando os outros fluxos, mais seguros, não puderem ser usados.

> [!Important]
> * O ponto de extremidade do Azure AD v2.0 oferece suporte apenas a locatários do ROPC para Azure AD, não a contas pessoais. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisam usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo do ROPC.

![Fluxo ROPC](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo ROPC é uma solicitação única &mdash;, envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe os tokens em retorno. O cliente deve solicitar o endereço de e-mail (UPN) e a senha do usuário antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&client_secret=wkubdywbc2894u
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parâmetro | Condição | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório no qual você deseja fazer o login. Pode estar no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `grant_type` | Obrigatório | Deve ser definido como `password`. |
| `username` | Obrigatório | Endereço de email do usuário. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendadas | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. Esses escopos devem receber consentimento antes do tempo por um administrador ou pelo usuário em um fluxo interativo. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo a seguir mostra um exemplo de uma resposta bem-sucedida de token:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formatar | DESCRIÇÃO |
| --------- | ------ | ----------- |
| `token_type` | Cadeia de caracteres | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso foi retornado, esse parâmetro lista os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | DESCRIÇÃO | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um suberror `consent_required` será retornado. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | O tipo de concessão não é suportado nos contextos de autenticação `/common` ou `/consumers`.  Use `/organizations` em vez disso. |
| `invalid_client` | O aplicativo está configurado incorretamente | Isso pode acontecer se a propriedade `allowPublicClient` não estiver configurada como verdadeiro no [manifesto do aplicativo](reference-app-manifest.md). A propriedade `allowPublicClient` é necessária porque a concessão de ROPC não possui um URI de redirecionamento. O Microsoft Azure Active Directory não pode determinar se o aplicativo é um aplicativo cliente público ou um aplicativo cliente confidencial, a menos que a propriedade esteja definida. Observe que o ROPC é suportado apenas para aplicativos clientes públicos. |

## <a name="learn-more"></a>Saiba mais

* Experimente ROPC por conta própria usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
