---
title: Azure Active Directory para desenvolvedores | Microsoft Docs
description: "Este artigo fornece uma visão geral sobre como conectar-se às contas corporativa e de estudante da Microsoft usando o Azure Active Directory."
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: c7375b91790cc7a089560bd823e99850e3b34fec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory para desenvolvedores
O Azure AD (Azure Active Directory) é um serviço de identidade de nuvem que permite aos desenvolvedores conectar com segurança qualquer usuário com uma conta corporativa ou de estudante da Microsoft. A documentação mostra como adicionar suporte do Azure AD para o seu aplicativo usando os protocolos padrão do setor: OAuth e OpenID Connect.

| | |
| --- | --- |
|[Noções básicas de autenticação](active-directory-authentication-scenarios.md) | Uma introdução à autenticação com o Azure AD. |
|[Tipos de aplicativos](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Uma visão geral dos cenários de autenticação com suporte no Azure AD. |                                
                                                                              
## <a name="get-started"></a>Introdução
As seguintes configurações interativas orientam você a usar as bibliotecas de autenticação da Microsoft para conectar os usuários no Azure AD.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicativos da área de trabalho e móveis](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Aplicativos da área de trabalho e móveis</center> | [Visão geral](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Aplicativos Web](./media/active-directory-developers-guide/Web_app.png)<br />Aplicativos Web</center> | [Visão geral](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Aplicativos de página única](./media/active-directory-developers-guide/SPA.png)<br />Aplicativos de página única</center> | [Visão geral](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![APIs da Web](./media/active-directory-developers-guide/Web_API.png)<br />APIs da Web</center> | [Visão geral](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![De serviços](./media/active-directory-developers-guide/Service_App.png)<br />Serviço a serviço</center> | [Visão geral](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Credenciais do cliente do OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="how-to-guides"></a>Guias de instruções
Os guias a seguir informam como executar tarefas comuns com o Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registro de aplicativo](active-directory-integrating-applications.md)           | Como registrar um aplicativo no Azure AD. |
|[Aplicativos multilocatários](active-directory-devhowto-multi-tenant-overview.md)    | Como entrar em qualquer conta corporativa da Microsoft. |
|[Protocolos OAuth e OpenID Connect](active-directory-protocols-openid-connect-code.md)| Como conectar usuários e chamar APIs Web usando os protocolos de autenticação da Microsoft. |
|[Guias adicionais](active-directory-developers-guide-index.md#guides)        |  Uma lista de guias que estão disponíveis para o Azure AD.   |

## <a name="reference-topics"></a>Tópicos de referência
Os artigos a seguir fornecem informações detalhadas sobre as APIs, as mensagens de protocolo e os termos usados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de autenticação (ADAL)](active-directory-authentication-libraries.md)   | Uma visão geral das bibliotecas e SDKs fornecidos pelo Azure AD. |
| [Exemplos de código](active-directory-code-samples.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](active-directory-dev-glossary.md)                                      | Terminologia e definições de palavras usadas em toda esta documentação. |
| [Tópicos de referência adicionais](active-directory-developers-guide-index.md#reference)| Uma lista de tópicos de referência que estão disponíveis para o Azure AD.   |


> [!NOTE]
> Se você precisar entrar nas contas pessoais da Microsoft, você talvez queira considerar o uso do [ponto de extremidade do Azure AD v 2.0](active-directory-appmodel-v2-overview.md). O ponto de extremidade do Azure AD v 2.0 é a unificação das contas pessoais da Microsoft e contas corporativas da Microsoft (do Azure AD) em um sistema de autenticação simples.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]