---
title: Bibliotecas de autenticação do Azure Active Directory v2.0 | Microsoft Docs
description: Bibliotecas de cliente compatíveis e bibliotecas de middleware de servidor, e links de origem, de biblioteca e de exemplos relacionados, para o ponto de extremidade do Azure Active Directory v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 1450cffca7a4cfa57856c75cdcc9e106958ea043
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600920"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação do Azure Active Directory v2.0

O [ponto de extremidade do Azure Active Directory (Azure AD) v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) dá suporte aos protocolos padrão do setor, OAuth 2.0 e OpenID Connect 1.0. A biblioteca de autenticação da Microsoft (MSAL) foi projetado para trabalhar com o ponto de extremidade do AD do Azure v 2.0. Também é possível usar bibliotecas de código-fonte aberto que oferecem suporte a OAuth 2.0 e OpenID Connect 1.0.

É recomendado que você use bibliotecas escritas por especialistas em domínio de protocolo que seguem uma metodologia SDL (Security Development Lifecycle), como [ a que é seguida pela Microsoft ][Microsoft-SDL]. Se você optar por suporte para os protocolos de código de mão, siga uma metodologia como SDL da Microsoft e pagamento fechar atenção para as considerações de segurança nas especificações padrões para cada protocolo.

> [!NOTE]
> Procurando as bibliotecas do Azure AD v 1.0 (ADAL)? Confira a [guia de biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Tipos de bibliotecas

O ponto de extremidade do Azure AD v 2.0 funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**. Os clientes e servidores nativos usam bibliotecas de cliente para obter tokens de acesso para chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**. Os aplicativos Web usam bibliotecas de middleware de servidor para a entrada do usuário. As APIs da Web usam bibliotecas de middleware de servidor para validar tokens enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte à biblioteca

Como você pode escolher uma biblioteca compatível com qualquer padrão quando você usa o ponto de extremidade v2.0, é importante saber onde obter suporte. Para problemas e solicitações de recursos no código de biblioteca, entre em contato com o proprietário da biblioteca. Para problemas e solicitações de recursos na implementação do protocolo do lado do serviço, entre em contato com a Microsoft. [Uma solicitação de recurso](https://feedback.azure.com/forums/169401-azure-active-directory) para recursos adicionais que você gostaria de ver no protocolo. [Criar uma solicitação de suporte](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) se você encontrar um problema em que o ponto de extremidade do AD do Azure v 2.0 não é compatível com OAuth 2.0 ou OpenID Connect 1.0.

As bibliotecas vêm em duas categorias de suporte:

* **Com suporte da Microsoft**. A Microsoft fornece correções para essas bibliotecas e fez a devida diligência do SDL nessas bibliotecas.
* **Compatível**. A Microsoft testou estas bibliotecas em cenários básicos e confirmou que funcionam com o ponto de extremidade v2.0. A Microsoft não fornece correções para essas bibliotecas e não as analisou. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade v2.0, veja as próximas seções deste artigo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft

> [!IMPORTANT]
> As bibliotecas de versão prévia MSAL são adequadas para uso em um ambiente de produção. A Microsoft fornece o mesmo suporte ao nível de produção para essas bibliotecas que as bibliotecas de produção atuais (ADAL). Durante a visualização, espere alterações para a API MSAL, formato de cache interno e outros mecanismos dessas bibliotecas sem aviso prévio, você precisará levar junto com correções de bugs ou melhorias de recurso. Isso pode afetar seu aplicativo. Por exemplo, uma alteração para o formato de cache pode exigir que os usuários entrar novamente. Uma alteração na API pode exigir que você atualize o código. Quando a versão disponibilidade geral (GA) se torna disponível, deverá atualizar todos os aplicativos usando uma versão de visualização da biblioteca em seis meses, ou eles podem parar de funcionar.

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android | .NET da MSAL (versão prévia) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicativo de área de trabalho](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (versão prévia) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicativo de Página Única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (versão prévia) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicativo iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (versão prévia) | [O Repositório Central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicativo Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor com suporte da Microsoft

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[Aplicativo MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Middleware OWIN OAuth Bearer para Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | Manipulador JWT para .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware OpenID Connect para ASP.NET |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplicativo MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Middleware OAuth Bearer para ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Manipulador JWT para .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicativo Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis

| Plataforma | Nome da biblioteca | Versão testada | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Conteúdo relacionado
Para saber mais sobre o ponto de extremidade do Azure AD v2.0, veja a [Visão geral do modelo de aplicativo do Azure AD v2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
