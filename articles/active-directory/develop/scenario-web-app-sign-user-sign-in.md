---
title: Aplicativo Web que conecta os usuários (entrar) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que usuários entrar (entrar)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074614"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplicativo que conecta os usuários Web - entrar

Saiba como adicionar as credenciais para o código para seu aplicativo web que os usuários entrar.

## <a name="sign-in"></a>Conexão

O código que temos no artigo anterior [configuração de código do aplicativo](scenario-web-app-sign-user-app-configuration.md) é tudo que você precisa para implementar o logout. Depois que o usuário entrou ao seu aplicativo, você provavelmente deseja habilitá-los fazer logoff. ASP.NET core cuida de saída para você.

## <a name="what-sign-out-involves"></a>O que fazer logoff envolve

Sair de um aplicativo web é sobre como mais de remover as informações sobre a conta conectada do estado do aplicativo web.
O aplicativo web também deve redirecionar o usuário para a plataforma de identidade de Microsoft v 2.0 `logout` ponto de extremidade para sair. Quando seu aplicativo web redireciona o usuário para o `logout` ponto de extremidade, esse ponto de extremidade limpa a sessão do usuário do navegador. Se seu aplicativo não acesse o `logout` ponto de extremidade, o usuário teria reautenticar no seu aplicativo sem inserir as credenciais novamente, porque eles teriam uma única entrada sessão válida com o ponto de extremidade do v 2.0 de plataforma do Microsoft Identity.

Para obter mais informações, consulte o [enviar uma solicitação de saída](v2-protocols-oidc.md#send-a-sign-out-request) seção o [v 2.0 de plataforma do Microsoft Identity e o protocolo OpenID Connect](v2-protocols-oidc.md) documentação conceitual.

## <a name="application-registration"></a>Registro de aplicativo

Durante o registro de aplicativo, você vai ter registrado uma **URI de logout de postagem**. Em nosso tutorial, você registrou `https://localhost:44321/signout-oidc` no **URL de logoff** campo da **configurações avançadas** seção o **autenticação** página. Para obter detalhes, consulte [ registrar o aplicativo do aplicativo Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Código do ASP.NET Core

### <a name="signout-button"></a>Botão Sair

Botão de saída é exposta no `Views\Shared\_LoginPartial.cshtml` e exibida apenas quando houver uma conta autenticada (ou seja, quando o usuário tenha entrado anteriormente).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` ação das `AccountController`

Pressionar o **saia** botão nos gatilhos de aplicativo web a `SignOut` ação no `Account` controlador. Nas versões anteriores dos modelos do ASP.NET core, o `Account` controlador foi inserido com o aplicativo web, mas isso não é mais o caso como é agora parte da estrutura do ASP.NET Core em si. 

O código para o `AccountController` está disponível no repositório de núcleo do ASP.NET em de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta:

- Define um OpenID redirecionar URI para `/Account/SignedOut` para que o controlador é chamado novamente quando o Azure AD executou de saída
- Chamadas `Signout()`, que permite que o middleware OpenIdConnect entre em contato com a plataforma de identidade Microsoft `logout` ponto de extremidade que:

  - Limpa o cookie de sessão do navegador, e
  - Chamadas, por fim, chama de volta a **URL de logoff**, que) por padrão, exibe assinado página de exibição [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) também é fornecido como parte do ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a chamada para o `logout` ponto de extremidade

O middleware do ASP.NET Core OpenIdConnect permite que seu aplicativo interceptar a chamada para a plataforma de identidade da Microsoft `logout` ponto de extremidade, fornecendo um evento OpenIdConnect chamado `OnRedirectToIdentityProviderForSignOut`. O aplicativo web usa para tentar evitar a caixa de diálogo Selecionar uma conta para ser apresentado ao usuário quando sair. Essa interceptação é feita na `AddAzureAdV2Authentication` do `Microsoft.Identity.Web` biblioteca reutilizável. Consulte [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Código do ASP.NET

No ASP.NET, saindo é disparado pelo método SignOut () em um controlador (por exemplo, AccountController). Esse método não faz parte da estrutura do ASP.NET (ao contrário do que acontece no ASP.NET Core) e não usa async e é por isso que ele:

- envia um desafio de saída do OpenId
- Limpa o cache
- redireciona para a página que ele deseja

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Se você não quiser usar o ASP.NET Core ou ASP.NET, você pode examinar a documentação do protocolo, que está disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-web-app-sign-user-production.md)
