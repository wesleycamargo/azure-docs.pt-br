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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b7a894ccd27ddcda2ab4b98c69333d37de077863
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156068"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Exemplos de código do Active Directory do Azure (ponto de extremidade V2)

Você pode usar o Microsoft Azure Active Directory (Azure AD) para:

- Adicionar autenticação e autorização para aplicativos Web e APIs da Web.
- Solicitar um token de acesso para acessar uma API da Web protegida.

Esse artigo descreve resumidamente e fornece links para exemplos para o ponto de extremidade do V2 do Azure AD. Esses exemplos mostram como isso é feito, junt com trechos de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. Comentários no código estão lá para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado em exemplos de V1, consulte [Exemplos de código do Azure AD (ponto de extremidade V1)](active-directory-code-samples.md).

Para entender o cenário básico para cada tipo de amostra, consulte [Tipos de aplicativo para o ponto de extremidade do Active Directory do Azure v2.0](active-directory-v2-flows.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos de desktop/móveis) que acessam o Microsoft Graph ou uma API da Web no nome de um usuário.

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API da Web ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)      | .NET/C#  | Interativo | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Móvel (UWP)   | .NET/C# (UWP) | Interativo | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Móveis (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interativo | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Móvel (iOS)       | iOS / Objective C ou swift | Interativo | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Móvel (Android)   | Android / Java | Interativo |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Aplicativos Web

Os exemplos a seguir ilustram os aplicativos Web que conectam usuários, chamam o Microsoft Graph ou chamam uma API da Web com a identidade do usuário.

 Plataforma | Conecta apenas usuários | Conecta usuários e chama o Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Aplicativos de daemon

Os exemplos a seguir mostram aplicativos desktop ou Web que acessam o Microsoft Graph ou uma API da Web com a identidade do aplicativo (sem usuário).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Aplicativo Web | .NET/C#  | Credenciais do cliente | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>SPA (Aplicativos de página única)

Este exemplo mostra como escrever um aplicativo de página única protegido com o AD do Azure.

 Plataforma |  Chama o Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [javascript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [javascript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-ad"></a>API da Web protegida pelo Microsoft Azure AD

O exemplo a seguir mostra como proteger uma API da Web com o ponto de extremidade V2 do Azure AD.

Plataforma | Amostra | DESCRIÇÃO
 -------- | ------------------- | ---------------------
Node.js | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Chama uma API da Web do ASP.NET Core de um aplicativo WPF usando o V2 do Azure AD.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API da Web chamando o Microsoft Graph ou outra API da Web

Esse exemplo ainda não está disponível.

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a API do Microsoft Graph, incluindo autenticação com o Microsoft Azure AD, consulte [Exemplos e tutoriais da Comunidade do Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Conceitos e referência da API do Graph do Microsoft Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
