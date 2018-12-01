---
title: Exemplos de código do Azure Active Directory | Microsoft Docs
description: Fornece um índice de exemplos de código disponíveis do Active Directory do Azure (ponto de extremidade V2), organizados por cenário.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b6dd80f649b0c92aacdb5711e88d7710db5f51b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424435"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Exemplos de código do Azure Active Directory (ponto de extremidade v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Você pode usar a plataforma de identidade da Microsoft para:

- Adicionar autenticação e autorização para aplicativos Web e APIs da Web.
- Solicitar um token de acesso para acessar uma API da Web protegida.

Este artigo descreve resumidamente e fornece links para exemplos do ponto de extremidade v2.0 do Azure AD. Esses exemplos mostram como isso é feito, junt com snippets de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. Comentários no código estão lá para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se estiver interessado em exemplos do v1.0, confira [Exemplos de código do Azure AD (ponto de extremidade v1.0)](sample-v1-code.md).

Para entender o cenário básico para cada tipo de amostra, consulte [Tipos de aplicativo para o ponto de extremidade do Active Directory do Azure v2.0](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>SPA (aplicativos de página única)

Esses exemplos mostram como codificar um aplicativo de página única protegido com o Azure AD. Os exemplos usam um dos sabores do MSAL.js:

* [Biblioteca de Autenticação da Microsoft para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Biblioteca de Autenticação da Microsoft para Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [Biblioteca de Autenticação da Microsoft para AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Plataforma |  Chama o Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Aplicativos Web

Os exemplos a seguir ilustram os aplicativos Web que iniciam sessão de usuários. Alguns exemplos também demonstram o aplicativo chamando o Microsoft Graph ou sua própria API Web com a identidade do usuário.

 Plataforma | Conecta apenas usuários | Conecta usuários e chama o Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.1 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) | Mesmo exemplo na [aspnetcore2-2-signInAndCallGraph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/aspnetcore2-2-signInAndCallGraph) branch
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [Início rápido do ASP.NET](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <p/>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Início rápido do Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos clientes públicos (aplicativos de desktop/móveis) que acessam a API do Microsoft Graph ou sua própria API Web no nome de um usuário. Todos esses aplicativos cliente usam MSAL (Bibliotecas de Autenticação da Microsoft).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API da Web ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Desktop (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interativo | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NET.png) | Autenticação Integrada do Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Nome de usuário/senha |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Móvel (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | Interativo |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Móveis (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Móvel (iOS)       | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interativo |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Móvel (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interativo |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Aplicativos de daemon

Os exemplos a seguir mostram um aplicativo que acessa a API do Microsoft Graph com sua própria identidade (sem usuários).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph
------------------ | -------- | ---------- | --------------------
Console | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | Credenciais do cliente | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Aplicativo Web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Credenciais do cliente | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo a seguir mostra um aplicativo cliente público em execução em um dispositivo sem um navegador da Web. O aplicativo pode ser uma ferramenta de linha de comando ou pode estar em execução no Linux / Mac ou no aplicativo IoT. O exemplo apresenta um aplicativo que acessa a API do Microsoft Graph em nome de um usuário que faz entra interativamente em outro dispositivo (por exemplo, um celular). Esse aplicativo cliente usa MSAL (Bibliotecas de Autenticação da Microsoft).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph
------------------ | -------- |  ----------| ----------
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>APIs da Web

O exemplo a seguir mostra como proteger uma API Web com o ponto de extremidade v2.0 do Azure AD. Essa API é utilizada por um aplicativo WPF, mas pode ser chamada por qualquer aplicativo.

Plataforma | Amostra
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (serviço) do [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a API do Microsoft Graph, incluindo autenticação com o Microsoft Azure AD, consulte [Exemplos e tutoriais da Comunidade do Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](v1-overview.md)

[Conceitos e referência da API do Graph do Microsoft Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
