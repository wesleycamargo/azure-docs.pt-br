---
title: Sobre a v2.0 | Azure
description: Saiba sobre a plataforma e o ponto de extremidade v2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 265d34c91a8c803256e718899f5b6ce2738a88e5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956418"
---
# <a name="about-v20"></a>Sobre a v2.0

A plataforma e o ponto de extremidade v2.0 estavam em versão prévia e foram aprimorados continuamente. Atualmente, os cenários de SPA (aplicativo de página única) do JavaScript tem recursos completos, e convidamos você a usar MSAL.js para criar aplicativos baseados em navegador e enviar seus comentários. Assim, podemos atualizar o status de versão prévia para GA (disponibilidade geral).

> [!NOTE]
> MSAL Android, iOS e .NET ainda têm recursos em desenvolvimento. Você pode usá-los para criar aplicativos e envie-nos comentários.

A experiência do desenvolvedor do portal do Azure foi atualizada significativamente para incluir todos os seus aplicativos criados com ADAL ou MSAL e para melhorar a usabilidade.

No passado, um desenvolvedor de aplicativos que quisesse dar suporte a contas da Microsoft pessoais e contas corporativas do Azure AD (Azure Active Directory) precisava fazer a integração com dois sistemas separados. O ponto de extremidade e a plataforma v2.0 fornecem uma versão de API de autenticação que simplifica esse processo. Ele permite conexão com os dois tipos de conta por meio de uma integração simples. Os aplicativos que usam o ponto de extremidade v2.0 também podem consumir as APIs REST da [API do Microsoft Graph](https://graph.microsoft.io) usando os dois tipos de conta.

## <a name="getting-started"></a>Introdução

Escolha sua plataforma de favorita na lista a seguir para criar um aplicativo usando as estruturas e bibliotecas de software livre da Microsoft. Você também pode usar os protocolos OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem usar uma biblioteca de autenticação.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Saiba mais sobre a plataforma e o ponto de extremidade v2.0

Saiba mais sobre o que você pode fazer com o ponto de extremidade v2.0 do Azure AD:

* Descobrir os [tipos de aplicativos que você pode criar com o ponto de extremidade v2.0 do Azure AD](v2-app-types.md).
* Entender as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0 do Azure AD.
* Assista a este vídeo para obter uma visão geral do ponto de extremidade v2.0 do Azure AD:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Recursos adicionais

Explore informações detalhadas sobre a v2.0:

* [Referência de protocolos da v2.0](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [`id_tokens` referência](id-tokens.md)
* [Referência de bibliotecas de autenticação da v2.0](reference-v2-libraries.md)
* [Escopos e consentimento na v2.0](v2-permissions-and-consent.md)
* [A API do Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Se você precisa apenas entrar em contas corporativas e de estudante usando o Azure Active Directory, comece com o [guia do desenvolvedor do Azure AD](v1-overview.md). O ponto de extremidade v2.0 destina-se ao uso por desenvolvedores que precisam explicitamente entrar em contas pessoais da Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
