---
title: "Bibliotecas de autenticação do Azure Active Directory v2.0 | Microsoft Docs"
description: "Bibliotecas de cliente compatíveis e bibliotecas de middleware de servidor, e links de origem, de biblioteca e de exemplos relacionados, para o ponto de extremidade do Azure Active Directory v2.0."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 5ba85485737a6f1ee8908834a74d34863429aeca
ms.openlocfilehash: e68a83936e6ae0b7349444cb24bafd6908166945


---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticação do Azure Active Directory v2.0
O ponto de extremidade do Azure Active Directory (Azure AD) v2.0 dá suporte aos protocolos padrão do setor, OAuth 2.0 e OpenID Connect 1.0. Você pode usar várias bibliotecas da Microsoft e de outras organizações com o ponto de extremidade v2.0.

Quando você compila um aplicativo que usa o ponto de extremidade v2.0, é recomendável usar bibliotecas escritas por especialistas em domínio de protocolo que seguem uma metodologia SDL (Security Development Lifecycle), como [a seguida pela Microsoft][Microsoft-SDL]. Se você codificar suporte para os protocolos manualmente, recomendamos que você siga a metodologia SDL e preste muita atenção nas considerações de segurança encontradas nas especificações dos padrões de cada um dos protocolos.

## <a name="types-of-libraries"></a>Tipos de bibliotecas
O Azure AD v 2.0 funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**. Os clientes e servidores nativos usam bibliotecas de cliente para obter tokens de acesso para chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**. Os aplicativos Web usam bibliotecas de middleware de servidor para a entrada do usuário. As APIs da Web usam bibliotecas de middleware de servidor para validar tokens enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte à biblioteca
Como você pode escolher uma biblioteca compatível com qualquer padrão quando você usa o ponto de extremidade v2.0, é importante saber onde obter suporte. Para problemas e solicitações de recursos no código de biblioteca, entre em contato com o proprietário da biblioteca. Para problemas e solicitações de recursos na implementação do protocolo do lado do serviço, entre em contato com a Microsoft.

As bibliotecas vêm em duas categorias de suporte:

* **Com suporte da Microsoft**. A Microsoft fornece correções para essas bibliotecas e fez a devida diligência do SDL nessas bibliotecas.
* **Compatível**. A Microsoft testou estas bibliotecas em cenários básicos e confirmou que funcionam com o ponto de extremidade v2.0. A Microsoft não fornece correções para essas bibliotecas e não as analisou. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade v2.0, veja as próximas seções deste artigo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft
| Plataforma | Nome da biblioteca | Baixar | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Windows Store, Xamarin |MSAL (biblioteca de autenticação da Microsoft) para .NET |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL para .NET (GitHub)][ClientLib-NET-Repo] |[Exemplo de cliente nativo da área de trabalho do Windows][ClientLib-NET-Sample] |
| Node.js |Plug-in Passport.js do Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |Em breve |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor com suporte da Microsoft
| Plataforma | Nome da biblioteca | Baixar | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |Middleware OWIN OpenID Connect para ASP.NET |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Projeto Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Exemplo de aplicativo Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |Middleware OWIN OAuth Bearer para ASP.Net |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Projeto Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Exemplo de API Web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |Middleware OWIN OpenID Connect para .NET Core |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Exemplo de aplicativo Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |Middleware OWIN portador OAuth para .net Core |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Segurança do ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |Em breve |
| Node.js |Plug-in Passport.js do Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Exemplo de aplicativo Web][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis
| Plataforma | Nome da biblioteca | Versão testada | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-ios.md) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | Em breve |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |Em breve |
| PHP | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | Em breve |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |Em breve |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |Em breve |

<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de middleware de servidor compatíveis
Em breve

## <a name="related-content"></a>Conteúdo relacionado
Para saber mais sobre o ponto de extremidade do Azure AD v2.0, veja a [Visão geral do modelo de aplicativo do Azure AD v2.0][AAD-App-Model-V2-Overview].

Para nos ajudar a refinar e moldar o nosso conteúdo, use o recurso de comentários do tópico de discussão no final deste artigo para fazer comentários.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
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
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
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
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/



<!--HONumber=Nov16_HO5-->


