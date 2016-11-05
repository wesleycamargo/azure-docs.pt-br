---
title: Bibliotecas v2.0 do Azure Active Directory | Microsoft Docs
description: Fornece uma lista de todas as bibliotecas de cliente compatíveis e bibliotecas de middleware de servidor, juntamente com links de origem/biblioteca/exemplos relacionados, para o ponto de extremidade v2.0 do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: skwan
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2016
ms.author: skwan;bryanla

---
# <a name="azure-active-directory-(ad)-v2.0-and-authentication-libraries"></a>Bibliotecas de autenticação do Azure AD (Active Directory) v2.0
O ponto de extremidade do v2.0 do Azure AD dá suporte aos protocolos padrão do setor, OAuth 2.0 e OpenID Connect 1.0.  Várias bibliotecas da Microsoft e de outras fontes podem ser usadas com o ponto de extremidade v2.0.

Ao criar um aplicativo que usa o ponto de extremidade v2.0, recomendamos que você use bibliotecas escritas por especialistas de domínio de protocolo que seguem uma metodologia SDL (Security Development Lifecycle), [como a seguida pela Microsoft][Microsoft-SDL].  Se você codificar suporte para os protocolos manualmente, recomendamos que você siga o SDL e pondere com cuidado as considerações de segurança encontradas nas especificações dos padrões de cada um dos protocolos.

## <a name="types-of-libraries"></a>Tipos de bibliotecas
Há dois tipos de bibliotecas que funcionam com v2.0: 

* **Bibliotecas de cliente**: bibliotecas de cliente são usadas nos clientes nativos e nos servidores para obter tokens de acesso a fim de chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**: bibliotecas de middleware de servidor são usadas por aplicativos Web para conectar um usuário e pelas APIs Web para validar tokens que são enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte à biblioteca
Como você pode escolher uma biblioteca compatível com qualquer padrão ao usar o ponto de extremidade v2.0, é importante saber onde obter suporte.  Problemas e solicitações de recursos no código de biblioteca vão para o proprietário da biblioteca. Problemas e solicitações de recursos na implementação do protocolo do lado do serviço vão para a Microsoft.

As bibliotecas vêm em duas categorias de suporte:

* **Com suporte da Microsoft**: a Microsoft fornece correções para essas bibliotecas.  A Microsoft fez auditoria detalhada de Security Development Lifecycle nessas bibliotecas. 
* **Compatível**: a Microsoft testou um conjunto de bibliotecas em cenários básicos e confirmou que funcionam com o ponto de extremidade v2.0.  A Microsoft não fornece correções para essas bibliotecas e não as analisou.  Problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade v2.0, consulte as seções a seguir. 

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft
| Plataforma | Nome da biblioteca | Baixar | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Windows Store, Xamarin |MSAL (biblioteca de autenticação da Microsoft) para .NET |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL for .NET (GitHub)][ClientLib-NET-Repo] |[Exemplo de cliente nativo da área de trabalho do Windows][ClientLib-NET-Sample] |
| Node.js |Plug-in Passport.js do Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |Em breve |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor com suporte da Microsoft
| Plataforma | Nome da biblioteca | Baixar | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |Middleware OWIN OpenID Connect para ASP.NET |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Projeto Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Aplicativo Web de exemplo][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |Middleware OWIN OAuth Bearer para ASP.Net |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Projeto Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Exemplo de API da Web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |Middleware OWIN OpenID Connect para .NET Core |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Segurança do ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplicativo Web de exemplo][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |Middleware OWIN portador OAuth para .net Core |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Segurança do ASP.Net (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |Em breve |
| Node.js |Plug-in Passport.js do Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Aplicativo Web de exemplo][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis
| Plataforma | Nome | Versão testada | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exemplo de aplicativo nativo](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |Em breve |
| Python – Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |Em breve |
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
Confira a [Visão geral do modelo de aplicativo do Azure AD v2][AAD-App-Model-V2-Overview] para saber mais sobre o ponto de extremidade V2.0 do Azure AD. 

Use a seção de comentários do Disqus a seguir para fornecer seus comentários e ajudar a refinar e a moldar o nosso conteúdo.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:

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




<!--HONumber=Oct16_HO2-->


