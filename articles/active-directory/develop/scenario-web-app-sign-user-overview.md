---
title: Aplicativo Web que conecta os usuários (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que conecta os usuários (visão geral)
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
ms.openlocfilehash: b46df5e4ec8352b47f744a507fad702c37aa5fba
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075094"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Aplicativo Web que conecta os usuários

Aprenda tudo o que precisa para compilar um aplicativo web que os usuários entrar com a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se você quiser criar seus primeiros aplicativos de web (ASP.NET Core) portátil que a entrada de usuários, siga este guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido: Aplicativo web ASP.NET Core que os usuários entrar](quickstart-v2-aspnet-core-webapp.md)

Se você preferir permanecer com o ASP.NET, experimente o tutorial a seguir:

> [!div class="nextstepaction"]
> [Início Rápido: Aplicativo web ASP.NET Core que os usuários entrar](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Visão geral

Você pode adicionar autenticação ao seu aplicativo web, para que ele pode conectar usuários. A adição da autenticação permite que seu aplicativo web para acessar informações de perfil limitado e, por exemplo, personalizar a experiência de que oferecer a seus usuários. Aplicativos Web autenticam um usuário em um navegador da web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para que ele entre no Azure AD. O Azure AD retorna uma resposta de logon por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Os usuários de entrar em aproveitar o [Open ID Connect](./v2-protocols-oidc.md) protocolo padrão em si simplificadas pelo uso de middleware [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Usuários de entrar do aplicativo de Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase, você também pode habilitar seu aplicativo para chamar as APIs da Web em nome do usuário conectado. Esta próxima fase é um cenário diferente, o que você encontrará no [aplicativo Web chama as APIs da Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Adicionar entrada a uma web app é sobre como proteger o aplicativo web e validar um token de usuário, que é o que **middleware** fazer bibliotecas. Esse cenário não requer ainda a autenticação bibliotecas MSAL (Microsoft), que são sobre como adquirir um token para chamar as APIs de protegidas. As bibliotecas de autenticação só serão introduzidas no cenário de acompanhamento quando o aplicativo web precisa chamar APIs da web.

## <a name="specifics"></a>Informações específicas

- Durante o registro de aplicativo, você precisará fornecer um ou vários (se você implantar seu aplicativo em vários locais) URIs de resposta. Em alguns casos (ASP.NET/ASP.NET núcleos), você precisará habilitar o token. Por fim, você desejará configurar um URI de logout, de modo que seu aplicativo reage aos usuários de assinatura-out.
- No código para seu aplicativo, você precisará fornecer a autoridade para o qual você aplicativo delegados entrada na web. Você talvez queira personalizar a validação de token (em especial em cenários de ISV).
- Aplicativos Web dão suporte a quaisquer tipos de conta. Para obter mais informações, consulte [suporte para tipos de conta](v2-supported-account-types.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-web-app-sign-user-app-registration.md)
