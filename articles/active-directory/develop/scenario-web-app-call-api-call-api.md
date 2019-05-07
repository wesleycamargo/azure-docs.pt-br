---
title: Aplicativo Web que chamadas de web APIs (chamando uma API da web) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs (chamando uma API da web) da web
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074629"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Aplicativo Web que chama APIs - web chamar uma API web

Agora que você tem um token, você pode chamar uma API web protegida.

## <a name="aspnet-core"></a>ASP.NET Core

Aqui está um código simplificado da ação do `HomeController`. Esse código obtém um token para chamar o Microsoft Graph. Esse código de tempo foi adicionado, que mostra como chamar o Microsoft Graph como uma API REST.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Você pode usar o mesmo princípio para chamar qualquer API da web.
>
> APIs de web mais do Azure fornecem um SDK que simplifica a chamá-lo. Isso também é o caso do Microsoft Graph. Você aprenderá no próximo artigo onde encontrar um tutorial que ilustra esses aspectos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-web-app-call-api-production.md)
