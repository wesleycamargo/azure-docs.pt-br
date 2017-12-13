---
title: Ponto de extremidade do Azure Active Directory v.2.0 | Microsoft Docs
description: "Uma introdução à criação de aplicativos com conexão à conta da Microsoft e ao Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bd090450fad0be855240788c4cfa9dc58c1c4c6d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Fazer logon de usuários da Conta da Microsoft e do Azure Active Directory em um mesmo aplicativo
No passado, um desenvolvedor de aplicativos que quisesse dar suporte a contas da Microsoft pessoas e contas profissionais do Azure Active Directory precisava fazer a integração com dois sistemas separados. O ponto de extremidade v2.0 do Azure AD (Azure Active Directory) apresenta uma nova versão de API de autenticação que simplifica esse processo. O ponto de extremidade v2.0 do Azure AD permite entrar com os dois tipos de conta por meio de uma integração simples. Os aplicativos que usam o ponto de extremidade v2.0 do Azure AD também podem consumir as APIs REST da [API do Microsoft Graph](https://graph.microsoft.io) usando os dois tipos de conta.

## <a name="getting-started"></a>Introdução
Escolha sua plataforma de favorita na lista a seguir para criar um aplicativo usando as estruturas e bibliotecas de software livre da Microsoft. Você também pode usar os protocolos OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem usar uma biblioteca de autenticação.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Saiba mais sobre o ponto de extremidade v2.0 do Azure AD
Saiba mais sobre o que você pode fazer com o ponto de extremidade v2.0 do Azure AD:

* Descobrir os [tipos de aplicativos que você pode criar com o ponto de extremidade v2.0 do Azure AD](active-directory-v2-flows.md).
* Entender as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0 do Azure AD.
* Assista a este vídeo para obter uma visão geral do ponto de extremidade v2.0 do Azure AD:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Recursos adicionais
Explore as informações detalhadas sobre a plataforma do ponto de extremidade v2.0 do Azure AD:

* [Referência de protocolos do Azure AD v2.0](active-directory-v2-protocols.md)
* [Referência de tokens do Azure AD v 2.0](active-directory-v2-tokens.md)
* [Referência de bibliotecas de autenticação do Azure AD v 2.0](active-directory-v2-libraries.md)
* [Escopos e Consentimento no ponto de extremidade v2.0 do Azure AD](active-directory-v2-scopes.md)
* [A API do Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Se você precisa apenas entrar em contas corporativas e de estudante usando o Azure Active Directory, comece com o [guia do desenvolvedor do Azure AD](active-directory-developers-guide.md). O ponto de extremidade v2.0 do Azure AD destina-se ao uso por desenvolvedores que precisam explicitamente entrar em contas pessoais da Microsoft.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
