---
title: Exemplos de código do Azure Active Directory | Microsoft Docs
description: Fornece um índice de amostras de código do Azure Active Directory (ponto de extremidade v1), organizado por cenário.
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
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5d7f0d1fc32f18991be6614bb7661b63570a8700
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264915"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Amostras de código do Azure Active Directory (terminal V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web.

Esta seção fornece links para exemplos que você pode usar para aprender mais sobre o ponto de extremidade do Azure AD V1. Esses exemplos mostram como isso é feito, junt com trechos de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado nos exemplos de código do Azure AD V2, consulte [v 2.0 exemplos de código por cenário](active-directory-v2-code-samples.md).

Para entender o cenário básico para cada tipo de exemplo, consulte [Cenários de Autenticação do Azure AD](active-directory-authentication-scenarios.md).

Você também pode contribuir com nossas amostras no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicativos de desktop e mobile cliente público chamar Microsoft Graph ou uma API da Web

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos de desktop/móveis) que acessam o Microsoft Graph ou uma API da Web no nome de um usuário.

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API da Web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | .NET/C# | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvel (UWP)            | .NET/C#  | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [armazenamento de windows dotnet](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (único locatário API da Web) </p> [dotnet webapi-multilocatário-windows-armazenamento](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (multilocatário API da Web)|
Móveis (Android, iOS, UWP)   | .NET/C# (Xamarin) | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | Android/Java | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Área de trabalho (Console)          | .NET/C# | Nome de usuário + senha </p> Autenticação Integrada do Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Área de trabalho (Console)           | .NET Core | Perfil do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Aplicativos Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicativos da Web, os usuários de assinatura, chamada Microsoft Graph, ou uma API da Web com a identidade do usuário

 Plataforma | Conecta apenas usuários | Gráfico AAD ou chamadas Microsoft Graph| Chama outra API da Web ASP.NET ou ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicativos da Web demonstrando o controle de acesso baseado em função (autorização)

Os exemplos a seguir mostram como implementar o controle de acesso baseado em função, que é usado para restringir as permissões de determinados recursos de um aplicativo web para determinados usuários. Os usuários estão autorizados dependendo se pertencerem a um grupo do AD do Azure ou da função.

Plataforma | Amostra | DESCRIÇÃO
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Um aplicativo web .NET 4.5 MVC que usa o AD do Azure **grupos** para autorização
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Um aplicativo web .NET 4.5 MVC que usa Azure AD **papéis** de autorização

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicativos de daemon (acessar APIs da Web com a identidade do aplicativo)

Os exemplos a seguir mostram aplicativos desktop ou Web que acessam o Microsoft Graph ou uma API da Web com a identidade do aplicativo (sem usuário).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API da Web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Aplicativo de daemon (Console)          | .NET/C#  | Credenciais do cliente com o segredo do aplicativo ou certificado | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicativo de daemon (Console)         | .NET Core | Credenciais do cliente com certificado| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Desktop            | Java | Credenciais do cliente |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
Aplicativo Web ASP.NET  | .NET/C# | Credenciais do cliente |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API da Web protegida pelo Active Directory do Azure

A amostra a seguir mostra como proteger uma API da Web node.js com o Azure AD.

Plataforma | Amostra | DESCRIÇÃO
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  Uma API Web NodeJS protegida usando o Azure AD e tokens de acesso do OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API da Web chamando o Microsoft Graph ou outra API da Web

Os exemplos a seguir demonstram uma API da web que chama outra API da web. O segundo exemplo mostra como tratar o acesso condicional.

 Plataforma |  Chama o Microsoft Graph | Chama outra API da Web ASP.NET ou ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Aplicativos de página única

Este exemplo mostra como escrever um aplicativo de página única protegido com o AD do Azure.

 Plataforma |  Chama o Microsoft Graph | Chama sua própria API | Chama outra API da Web
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para amostras e tutoriais que demonstram padrões de uso diferentes para a API do Microsoft Graph, incluindo a autenticação com o Azure AD, consulte [ Exemplos e tutoriais da comunidade do Microsoft Graph ](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Conceitos e referência da API do Graph do Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da API do Graph do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
