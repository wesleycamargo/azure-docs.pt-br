---
title: Aplicativo Web que conecta os usuários (configuração do código) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que conecta os usuários (configuração de código)
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
ms.openlocfilehash: b2204fe3e08b3c4b909ddc8b7ade4cec219d34fb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406625"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Que se conecta os usuários - configuração de código do aplicativo Web

Saiba como configurar o código do aplicativo Web que os usuários entrar.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas usadas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões de modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions para .NET usado diretamente pelo ASP.NET e ASP.NET Core, propõe a um conjunto de DLLs em execução no .NET Framework e .NET Core. De um aplicativo Web do Core ASP.NET/ASP.NET, você pode controlar a validação de token usando o **TokenValidationParameters** classe (em especial em alguns cenários de ISV) |

## <a name="aspnet-core-configuration"></a>Configuração do ASP.NET Core

### <a name="application-configuration-files"></a>Arquivos de configuração de aplicativo

No ASP.NET Core, uma Web entrar em usuários do aplicativo com a plataforma de identidade da Microsoft é configurado por meio de `appsettings.json` arquivo. As configurações que você precisa preencher são:

- a nuvem `Instance` se você quiser que seu aplicativo seja executado em nuvens nacionais
- o público-alvo no `tenantId`
- o `clientId` para seu aplicativo, como foi copiado do portal do Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

No ASP.NET Core, há outro arquivo que contém a URL (`applicationUrl`) e a porta SSL (`sslPort`) para seu aplicativo, bem como vários perfis.

```JSon
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

No portal do Azure, a resposta de URIs que você precisa registrar na **autenticação** página do seu aplicativo precisa corresponder a essas URLs; ou seja, para os dois arquivos de configuração acima, eles seria `https://localhost:44321/signin-oidc` como o applicationUrl está `http://localhost:3110` , mas o `sslPort` é especificado (44321) e o `CallbackPath` é `/signin-oidc` conforme definido no `appsettings.json`.
  
Da mesma forma, o URI de saída seria definida como `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Código de inicialização

Em aplicativos Web do ASP.NET Core (e APIs da Web), o código que fazer a inicialização do aplicativo está localizado no `Startup.cs` arquivo, e, para adicionar a autenticação com o Microsoft Identity platform (anteriormente conhecido como Azure AD) v2.0, você precisará adicionar o código a seguir. Os comentários no código devem ser autoexplicativos.

```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

No ASP.NET, o aplicativo é configurado por meio de `Web.Config` arquivo

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

O código relacionado à autenticação no aplicativo Web ASP.NET / APIs Web está localizada no `App_Start/Startup.Auth.cs` arquivo.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entrar e sair](scenario-web-app-sign-user-sign-in.md)
