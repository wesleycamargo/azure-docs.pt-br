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
ms.date: 10/17/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7323f532787fa767d875e13262cce9f1ceaa79d8
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955499"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Exemplos de código do Active Directory do Azure (ponto de extremidade V2)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Você pode usar o Microsoft Azure Active Directory (Azure AD) para:

- Adicionar autenticação e autorização para aplicativos Web e APIs da Web.
- Solicitar um token de acesso para acessar uma API da Web protegida.

Esse artigo descreve resumidamente e fornece links para exemplos para o ponto de extremidade do V2 do Azure AD. Esses exemplos mostram como isso é feito, junt com snippets de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. Comentários no código estão lá para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado em exemplos de V1, consulte [Exemplos de código do Azure AD (ponto de extremidade V1)](sample-v1-code.md).

Para entender o cenário básico para cada tipo de amostra, consulte [Tipos de aplicativo para o ponto de extremidade do Active Directory do Azure v2.0](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>SPA (aplicativos de página única)

Este exemplo mostra como escrever um aplicativo de página única protegido com o Azure AD. Estes exemplos usam uma das opções de MSAL.js: [Biblioteca de Autenticação da Microsoft para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [Biblioteca de Autenticação da Microsoft para Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [Biblioteca de Autenticação da Microsoft para AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Plataforma |  Chama o Microsoft Graph
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Aplicativos Web

Os exemplos a seguir ilustram os aplicativos Web que iniciam sessão de usuários. Alguns exemplos também demonstram o aplicativo chamando o Microsoft Graph ou sua própria API Web com a identidade do usuário.

 Plataforma | Conecta apenas usuários | Conecta usuários e chama o Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos públicos de clientes (aplicativos de desktop / móveis) que acessam o Microsoft Graph ou sua própria API da Web no nome de um usuário. Todos esses aplicativos de cliente usam Bibliotecas de Autenticação da Microsoft (MSAL)

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API da Web ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Desktop (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interativo | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NET.png) | Autenticação Integrada do Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Nome de usuário/senha |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-up-v2)
Móvel (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | Interativo |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Móveis (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Móvel (iOS)       | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interativo |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Móvel (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interativo |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Aplicativos de daemon

O exemplo a seguir mostra um aplicativo que acessa o Microsoft Graph com sua própria identidade (sem usuários).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph
------------------ | -------- | ---------- | --------------------
Console | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | Credenciais do cliente | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Aplicativo Web | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Credenciais do cliente | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Aplicativos sem periféricos

Os exemplos a seguir mostram aplicativos clientes públicos em execução em um dispositivo sem um navegador da Web. Esses aplicativos podem ser ferramentas de linha de comando ou aplicativos em execução no Linux / Mac ou no aplicativo iOT. Os exemplos apresentam aplicativos que acessam o Microsoft Graph em nome de um usuário que faz login interativamente em outro dispositivo (por exemplo, um telefone celular). Todos esses aplicativos de cliente usam Bibliotecas de Autenticação da Microsoft (MSAL)

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph
------------------ | -------- |  ----------| ----------
Área de trabalho (Console)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>APIs da Web

O exemplo a seguir mostra como proteger uma API da Web com o ponto de extremidade V2 do Azure AD. Essa API é utilizada por um aplicativo WPF (mas na verdade poderia ser chamada por qualquer aplicativo)

Plataforma | Amostra
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a API do Microsoft Graph, incluindo autenticação com o Microsoft Azure AD, consulte [Exemplos e tutoriais da Comunidade do Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](v1-overview.md)

[Conceitos e referência da API do Graph do Microsoft Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
