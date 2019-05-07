---
title: Visão geral cenário do aplicativo de página única JavaScript – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (visão geral do cenário) que se integra a plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076354"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicativo de página única

Aprenda tudo o que você precisa para criar um aplicativo de página única (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Você pode criar seu primeiro aplicativo seguindo o guia de início rápido JavaScript SPA:

> [!div class="nextstepaction"]
> [Início Rápido: Aplicativo de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Visão geral

Muitos aplicativos web modernos são criados como aplicativos de página única do lado do cliente escritos usando JavaScript ou uma estrutura de SPA como Angular, VUE e React. js. Esses aplicativos executados em um navegador da web e têm características de autenticação diferente de aplicativos web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que os aplicativos de página única conectar usuários e obter tokens para acessar os serviços de back-end ou APIs web usando o [fluxo implícito do OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que o aplicativo obter tokens de ID para representar o usuário autenticado e também o necessário para chamar as APIs protegidas de tokens de acesso.

![Aplicativos de página única](./media/scenarios/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos usando estruturas de JavaScript de plataforma cruzada como Electron, React Native e assim por diante. já que eles exigem mais recursos para interação com plataformas nativas.

## <a name="specifics"></a>Informações específicas

Os seguintes aspectos são necessárias para habilitar esse cenário para o seu aplicativo:

* Registro de aplicativo com o AD do Azure envolve ativar o fluxo implícito e definindo um URI de redirecionamento para o qual os tokens são retornados.
* Configuração de aplicativo com as propriedades do aplicativo registrado como ID do aplicativo.
* Usando a biblioteca MSAL para fazer o fluxo de autenticação para entrar e adquirir tokens.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-spa-app-registration.md)
