---
title: Sobre a v2.0 | Azure
description: Saiba sobre a plataforma e o ponto de extremidade v2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1f707351f5f5887155148b58d5ed145dde785b40
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095405"
---
# <a name="about-v20"></a>Sobre a v2.0

A plataforma e o ponto de extremidade v2.0 estavam em versão prévia e foram aprimorados continuamente. Atualmente, os cenários de SPA (aplicativo de página única) do JavaScript tem recursos completos, e convidamos você a usar MSAL.js para criar aplicativos baseados em navegador e enviar seus comentários. Assim, podemos atualizar o status de versão prévia para GA (disponibilidade geral).

> [!NOTE]
> MSAL Android, iOS e .NET ainda têm recursos em desenvolvimento. Você pode usá-los para criar aplicativos e envie-nos comentários.

A experiência [Registros de aplicativo (versão prévia)](quickstart-register-app.md) do portal do Azure foi atualizada consideravelmente para incluir todos os seus aplicativos criados com ADAL ou MSAL e melhorar a usabilidade.

No passado, um desenvolvedor de aplicativos que quisesse dar suporte a contas da Microsoft pessoais e contas corporativas do Azure AD (Azure Active Directory) precisava fazer a integração com dois sistemas separados. O ponto de extremidade e a plataforma v2.0 fornecem uma versão de API de autenticação que simplifica esse processo. Ele permite conexão com os dois tipos de conta por meio de uma integração simples. Os aplicativos que usam o ponto de extremidade v2.0 também podem consumir as APIs REST da [API do Microsoft Graph](https://developer.microsoft.com/graph) usando os dois tipos de conta.

## <a name="getting-started"></a>Introdução

Escolha sua plataforma favorita na seguinte lista para criar um aplicativo usando as bibliotecas e as estruturas de software livre da Microsoft:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Saiba mais sobre a plataforma e o ponto de extremidade v2.0

Saiba mais sobre o que você pode fazer com o ponto de extremidade v2.0 do Azure AD:

* Descobrir os [tipos de aplicativos que você pode criar com o ponto de extremidade v2.0 do Azure AD](v2-app-types.md).
* Entender as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0 do Azure AD.

## <a name="additional-resources"></a>Recursos adicionais

Explore informações detalhadas sobre a v2.0:

* [Sobre a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md)
* [Referência de protocolos da v2.0](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação da v2.0](reference-v2-libraries.md)
* [Permissões e consentimento na v2.0](v2-permissions-and-consent.md)
* [API do Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Se você precisa apenas entrar em contas corporativas e de estudante usando o Azure Active Directory, comece com o [guia do desenvolvedor do Azure AD](v1-overview.md). O ponto de extremidade v2.0 destina-se ao uso por desenvolvedores que precisam explicitamente entrar em contas pessoais da Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
