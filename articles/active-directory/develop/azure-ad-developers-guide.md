---
title: Azure Active Directory para desenvolvedores | Microsoft Docs
description: Este artigo fornece uma visão geral sobre como conectar-se às contas corporativa e de estudante da Microsoft usando o Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591203"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory para desenvolvedores

O Azure Active Directory (Azure AD) é um serviço de identidade de nuvem que permite aos desenvolvedores criar aplicativos que conectam com segurança qualquer usuário com uma conta corporativa ou de estudante da Microsoft. O Azure Active Directory oferece tanto suporte a desenvolvedores que criam aplicativos de linha de negócios (LOB) com locatário único quanto a desenvolvedores que buscam desenvolver aplicativos de multilocação. Além da conexão básica, o Azure Active Directory também permite que aplicativos chamem as APIs Microsoft como [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) e as APIs personalizadas que são criadas na plataforma do Azure Active Directory. A documentação mostra como adicionar suporte ao Azure Active Directory para o seu aplicativo usando protocolos padrão como OAuth2.0 e OpenID Connect.

> [!NOTE]
> A maioria do conteúdo nessa página se concentra no ponto de extremidade do Azure Active Directory v1.0, que oferece suporte somente a contas corporativas ou de estudante da Microsoft. Se deseja entrar em contas da Microsoft do tipo consumidor ou pessoal, consulte mais informações no ponto de extremidade [Azure AD v2.0](active-directory-appmodel-v2-overview.md). O ponto de extremidade do Azure Active Directory v2.0 oferece uma experiência de desenvolvedor unificada para aplicativos que desejam conectar os usuários com contas do Azure Active Directory (corporativa e de estudante) e contas pessoais da Microsoft.

| | |
| --- | --- |
|[Noções básicas de autenticação](authentication-scenarios.md) | Uma introdução à autenticação com o Azure AD. |
|[Tipos de aplicativos](authentication-scenarios.md#application-types-and-scenarios) | Uma visão geral dos cenários de autenticação com suporte no Azure AD. |      
| | |

## <a name="get-started"></a>Introdução
As configurações guiadas a seguir orientam você durante a criação de um aplicativo em sua plataforma preferida usando o SDK da Biblioteca  Azure AD Authentication (ADAL). Se estiver procurando informações sobre como usar a Biblioteca de Autenticação da Microsoft (MSAL), consulte nossa documentação sobre o [ponto de extremidade do Azure Active Directory v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Aplicativos da área de trabalho e móveis](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Aplicativos da área de trabalho e móveis</center> | [Visão geral](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Aplicativos Web](./media/azure-ad-developers-guide/Web_app.png)<br />Aplicativos Web</center> | [Visão geral](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![Aplicativos de página única](./media/azure-ad-developers-guide/SPA.png)<br />Aplicativos de página única</center> | [Visão geral](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![APIs da Web](./media/azure-ad-developers-guide/Web_API.png)<br />APIs da Web</center> | [Visão geral](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![De serviços](./media/azure-ad-developers-guide/Service_App.png)<br />Serviço a serviço</center> | [Visão geral](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Guias de instruções
Esses guias orientam você entre algumas das tarefas mais comuns no Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registro de aplicativo](quickstart-v1-integrate-apps-with-azure-ad.md)           | Como registrar um aplicativo no Azure AD. |
|[Aplicativos multilocatários](howto-convert-app-to-be-multi-tenant.md)    | Como entrar em qualquer conta corporativa da Microsoft. |
|[Protocolos OAuth e OpenID Connect](v1-protocols-openid-connect-code.md)| Como conectar usuários e chamar APIs Web usando os protocolos de autenticação da Microsoft. |
|  |  |

## <a name="reference-topics"></a>Tópicos de referência
Os artigos a seguir fornecem informações detalhadas sobre as APIs, as mensagens de protocolo e os termos usados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de autenticação (ADAL)](active-directory-authentication-libraries.md)   | Uma visão geral das bibliotecas e SDKs fornecidos pelo Azure AD. |
| [Exemplos de código](sample-v1-code.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](developer-glossary.md)                                      | Terminologia e definições de palavras usadas em toda esta documentação. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
