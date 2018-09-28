---
title: Exemplos de código do Azure Active Directory | Microsoft Docs
description: Fornece um índice de exemplos de código do Azure Active Directory (ponto de extremidade v1.0), organizado por cenário.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a8d1c94ac4357c7833f6db7cb9b4eaffa00c0396
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963791"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Exemplos de código do Azure Active Directory (ponto de extremidade v1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web.

Esta seção fornece links para exemplos que você pode usar para aprender mais sobre o ponto de extremidade do Azure AD v1.0. Esses exemplos mostram como isso é feito, junt com snippets de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado nos exemplos de código do Azure AD V2, consulte [v 2.0 exemplos de código por cenário](sample-v2-code.md).

Para entender o cenário básico para cada tipo de exemplo, consulte [Cenários de Autenticação do Azure AD](authentication-scenarios.md).

Você também pode contribuir com nossas amostras no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Este exemplo mostra como escrever um aplicativo de página única protegido com o Azure AD.

 Plataforma | Chama sua própria API | Chama outra API da Web
 -------- |  --------------------- | ------------------ | ----------------
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Aplicativos Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicativos da Web, os usuários de assinatura, chamada Microsoft Graph, ou uma API da Web com a identidade do usuário

Os exemplos a seguir ilustram os usuários assinantes de aplicativos Web. Alguns desses aplicativos também chamam o Microsoft Graph, ou sua própria API Web, no nome do usuário conectado.

 Plataforma | Conecta apenas usuários | Gráfico AAD ou chamadas Microsoft Graph| Chama outra API da Web ASP.NET ou ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Php](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicativos da Web demonstrando o controle de acesso baseado em função (autorização)

Os exemplos a seguir mostram como implementar o RBAC (controle de acesso baseado em função). O RBAC é usado para restringir as permissões de determinados recursos em um aplicativo Web para determinados usuários. Os usuários estão autorizados dependendo se pertencerem a um **grupo do Azure AD** ou se têm uma determinada **função** do aplicativo.

Plataforma | Amostra
 -------- | -------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Um aplicativo web .NET 4.5 MVC que usa Azure AD **papéis** de autorização

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicativos de desktop e mobile cliente público chamar Microsoft Graph ou uma API da Web

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos da área de trabalho/móvel) que acessam o Microsoft Graph ou uma API Web no nome de um usuário. Dependendo dos dispositivos e plataformas, os aplicativos podem conectar usuários de diferentes maneiras (fluxos/concessões): 

- interativamente,
- silenciosamente (com Autenticação Integrada do Windows no Windows ou nome de usuário/senha), 
- ou até mesmo delegando uma entrada interativa para outro dispositivo (o fluxo de código do dispositivo usado em dispositivos que não fornecem controles da Web).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API Web ASP.NET ou ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interativo | Parte do [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvel (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Este exemplo usa [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), e não [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplicativo da UWP usando ADAL.NET para chamar uma API Web de locatário único) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplicativo da UWP usando ADAL.NET para chamar uma API Web de locatário múltiplo)|
Móveis (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Área de trabalho (Console)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Nome de usuário + senha </p>  Autenticação Integrada do Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Área de trabalho (Console)          | ![Console Java](media/sample-v2-code/logo_Java.png) | Nome de usuário + senha | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Área de trabalho (Console)           | ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Fluxo de código do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicativos de daemon (acessar APIs da Web com a identidade do aplicativo)

Os exemplos a seguir mostram aplicativos desktop ou Web que acessam o Microsoft Graph ou uma API da Web com a identidade do aplicativo (sem usuário).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama uma API da Web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Aplicativo de daemon (Console)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenciais do cliente com o segredo do aplicativo ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicativo de daemon (Console)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Credenciais do cliente com certificado| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplicativo Web ASP.NET  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Credenciais do cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API da Web protegida pelo Active Directory do Azure

A amostra a seguir mostra como proteger uma API da Web node.js com o Azure AD.

Nas seções anteriores deste artigo, você também pode encontrar outros exemplos que ilustram um aplicativo cliente **chamando** uma **API Web** ASP.NET ou ASP.NET Core. Esses exemplos não são mencionados novamente nesta seção, mas você encontrará na última coluna das tabelas acima ou abaixo

Plataforma | Amostra
 -------- | -------------------
![Php](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API da Web chamando o Microsoft Graph ou outra API da Web

Os exemplos a seguir demonstram uma API da web que chama outra API da web. O segundo exemplo mostra como tratar o acesso condicional.

 Plataforma |  Chama o Microsoft Graph | Chama outra API da Web ASP.NET ou ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para amostras e tutoriais que demonstram padrões de uso diferentes para a API do Microsoft Graph, incluindo a autenticação com o Azure AD, consulte [ Exemplos e tutoriais da comunidade do Microsoft Graph ](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](azure-ad-developers-guide.md)

[Bibliotecas de autenticação do Azure Active Directory](active-directory-authentication-libraries.md)

[Conceitos e referência da API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
