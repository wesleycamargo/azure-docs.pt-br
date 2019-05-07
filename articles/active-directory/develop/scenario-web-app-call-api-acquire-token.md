---
title: Aplicativo Web que chama APIs (adquirir um token para o aplicativo) - web plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs (adquirindo um token para o aplicativo) da web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074794"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplicativo Web que chama APIs - web adquirir um token para o aplicativo

Agora que você criou o objeto de aplicativo cliente, você o usará para adquirir um token que, em seguida, você usará para chamar uma APIs da web. No ASP.NET ou ASP.NET Core, chamando uma web que API, em seguida, é feita no controlador. É sobre:

- Obtendo um token para a API da web usando o cache de token. Para isso, você deve chamar `AcquireTokenSilent`
- Chamar a API protegida com o token de acesso

## <a name="aspnet-core"></a>ASP.NET Core

Os métodos do controlador são protegidos por um `[Authorize]` atributo que força os usuários sejam autenticados para usar o aplicativo Web. Aqui está o código que chama o Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Aqui está um código simplificado da ação de HomeController, que obtém um token para chamar o Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Se você quiser compreender em detalhes o total do código necessário para este cenário, consulte a fase 2 [2-1-Web App chamadas Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) etapa o [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) tutorial

Há várias complexidades adicionais, como:

- Implementando um cache de token para o aplicativo Web (tutorial apresentam várias implementações)
- Remover a conta do cache quando o usuário sinais-out
- Chamar várias APIs, incluindo ter consentimento incremental

## <a name="aspnet"></a>ASP.NET

As coisas são semelhantes no ASP.NET:

- Uma ação do controlador protegida por um atributo [autorizar], extrairá o ID de locatário e ID de usuário da `ClaimsPrincipal` membro do controlador (ASP.NET usa `HttpContext.User`)
- a partir daí, ele cria um MSAL.NET `IConfidentialClientApplication`
- Chamada IT o `AcquireTokenSilent` método do aplicativo cliente confidencial 

o código é semelhante ao código mostrado para o ASP.NET Core

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API da Web](scenario-web-app-call-api-call-api.md)
