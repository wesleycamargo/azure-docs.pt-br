---
title: Aplicativo Web que chamadas de web APIs (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que chamadas de web APIs (visão geral)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076294"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Cenário: Aplicativo Web que chamadas às APIs da web

Saiba como criar um aplicativo web de assinatura, os usuários na plataforma de identidade da Microsoft e que chama as APIs da web em nome do usuário conectado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Esse cenário supposes que você já verificou o cenário a seguir:

> [!div class="nextstepaction"]
> [Aplicativo de Web que os usuários entrar](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Visão geral

Adicionar autenticação ao seu aplicativo Web, que, portanto, pode conectar usuários e chama uma API da web em nome do usuário conectado.

![Aplicativo Web que chamadas às APIs da web](./media/scenario-webapp/web-app.svg)

Aplicativos Web que chama APIs web:

- São aplicativos cliente confidencial.
- é por isso que eles já registrou um segredo (senha de aplicativo ou certificado) com o Azure AD. Esse segredo é passado durante a chamada para o Azure AD para obter um token

## <a name="specifics"></a>Informações específicas

> [!NOTE]
> Adicionar entrada a um aplicativo Web não usa as bibliotecas MSAL, como se trata sobre como proteger o aplicativo Web. Proteção de bibliotecas é obtida com bibliotecas de Middleware de chamada. Isso era o objeto do cenário anterior [usuários entrar em um aplicativo Web](scenario-web-app-sign-user-overview.md)
>
> Ao chamar APIs da web de um aplicativo Web, você precisará obter tokens de acesso para essas APIs da web. Você pode usar bibliotecas MSAL para adquirir esses tokens.

Portanto, a experiência de ponta a ponta de desenvolvedores para esse cenário tem aspectos específicos, como:

- Durante o [registro de aplicativo](scenario-web-app-call-api-app-registration.md), você precisará fornecer um, ou várias (se você implantar seu aplicativo em vários locais) responder URIs, segredos, ou certificados precisam ser compartilhados com o Azure AD.
- O [configuração de aplicativo](scenario-web-app-call-api-app-configuration.md) precisará fornecer as credenciais de cliente como compartilhado com o Azure AD durante o registro de aplicativo

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-web-app-call-api-app-registration.md)
