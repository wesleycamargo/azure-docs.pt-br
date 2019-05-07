---
title: Aplicativo Web que chamadas de web (configuração do código) – APIs de plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chamadas de web APIs (configuração de código do aplicativo)
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
ms.openlocfilehash: 95a5e1ed89b6330a0b6a49cb20d8bf0ef3587d48
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074734"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplicativo Web que chama APIs - configuração de código de web

Como visto na [cenário de usuários entra no aplicativo Web](scenario-web-app-sign-user-overview.md), considerando que o usuário de assinatura é delegado ao Open ID connect (OIDC) middleware, convém gancho-up no processo de OIDC. A maneira de fazer isso é diferente dependendo da estrutura que você usou (aqui ASP.NET e ASP.NET Core), mas no final, você assina eventos do middleware OIDC. O princípio é que:

- Você vai permitir que ASP.NET ou ASP.NET core solicitar um código de autorização. Fazendo isso impedirá de núcleo ASP.NET/ASP.NET permitem que o usuário entrar e consentir,
- Você vai assinar o recebimento do código de autorização pelo aplicativo Web.
- Quando o código de autenticação é recebido, você usará bibliotecas MSAL para resgatar o código e os tokens de acesso resultante e repositório de tokens no cache de token de atualização. A partir daí, o cache pode ser usado em outras partes do aplicativo para adquirir outros tokens silenciosamente.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotecas que dão suporte a cenários de aplicativo Web

As bibliotecas que dão suporte a fluxo de código de autorização para aplicativos Web são:

| Biblioteca MSAL | DESCRIÇÃO |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plataformas com suporte são plataformas do .NET Framework e .NET Core (não UWP, xamarin. IOS e xamarin. Android como essas plataformas são usados para criar aplicativos de cliente público) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em andamento - em visualização pública |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em andamento - em visualização pública |

## <a name="aspnet-core-configuration"></a>Configuração do ASP.NET Core

No ASP.NET Core, as coisas que acontecem no `Startup.cs` arquivo. Você vai querer assinar o `OnAuthorizationCodeReceived` abrir a ID de evento connect e desse evento, chame o MSAL. Método do NET `AcquireTokenFromAuthorizationCode` que tem o efeito de armazenar em cache de token, o token de acesso para os escopos solicitados e um token de atualização que será usado para atualizar o token de acesso quando ele estiver perto de expiração, ou para obter um token em nome do usuário mesmo , mas para um recurso diferente.

Os comentários no código a seguir o ajudará a entender alguns aspectos complicados da mesma MSAL.NET e ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

ASP.NET Core, Compilando o aplicativo cliente confidencial usa as informações que está no HttpContext. Este HttpContext Saiba sobre a URL para o aplicativo Web e o usuário conectado (em um `ClaimsPrincipal`). Ele também usa a configuração do ASP.NET Core, que tem uma seção de "AzureAD", e que é associado a `_applicationOptions` estrutura de dados. Por fim, o aplicativo precisa manter os caches de token.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` realmente resgata o código de autorização solicitado pelo ASP.NET e obtém os tokens que são adicionados ao cache de token de usuário MSAL.NET. A partir daí, em seguida, eles são usados nos controladores ASP.NET Core.

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

Maneira como o ASP.NET manipula coisas é semelhante, exceto que a configuração do OpenIdConnect e a assinatura para o `OnAuthorizationCodeReceived` evento que acontece no `App_Start\Startup.Auth.cs` arquivo. Você encontrará os conceitos semelhantes, exceto que, aqui você precisará especificar o RedirectUri no arquivo de configuração, que é um pouco menos robusta:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
  NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache de Token MSAL.NET para um aplicativo Web do ASP.NET (núcleos)

Em aplicativos web (ou APIs como uma questão de fato da web), a implementação de cache de token é diferente do que as implementações de cache de token de aplicativos da área de trabalho (que são frequentemente [baseada em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache). Ele pode usar a sessão ASP.NET/ASP.NET Core, ou um cache Redis, ou um banco de dados ou até mesmo armazenamento de Blog do Azure. No código do trecho de código acima é o objeto do `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` chamada de método, que associa um serviço de cache. Os detalhes sobre o que acontece aqui está além do escopo deste guia de cenário, mas os links são fornecidos abaixo.

> [!IMPORTANT]
> Um ponto importante a observar é que para aplicativos web e APIs da web, deve haver um cache de token por usuário (por conta). Você precisa serializar o cache de token para cada conta.

Exemplos de como usar os caches de token para aplicativos Web e APIs web estão disponíveis na [tutorial do aplicativo Web do ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) na fase [Cache de Token 2 de 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Para implementações têm uma olhada na seguinte pasta [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) na [microsoft extensões de autenticação para dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) library (no [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) pasta.

## <a name="next-steps"></a>Próximas etapas

Neste ponto, quando o usuário entrar um token é armazenado no cache de token. Vamos ver como ele é usado em outras partes do aplicativo Web.

> [!div class="nextstepaction"]
> [Entrar no aplicativo Web](scenario-web-app-call-api-sign-in.md)
