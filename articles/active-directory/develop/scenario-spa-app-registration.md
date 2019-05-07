---
title: Aplicativo de página única (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (registro de aplicativo)
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074824"
---
# <a name="single-page-application---app-registration"></a>Aplicativo de página única - registro de aplicativo

Esta página explica as especificidades de registro de aplicativo para um aplicativo de página única (SPA).

Siga as etapas a serem [registrar um novo aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md)e selecione as contas com suporte para o seu aplicativo. O cenário SPA pode dar suporte à autenticação com contas em sua organização ou qualquer organização e contas pessoais da Microsoft.

Em seguida, aprenda os aspectos específicos de registro de aplicativo que se aplicam a aplicativos de única página.

## <a name="register-a-redirect-uri"></a>Registrar um URI de redirecionamento

O fluxo implícito envia os tokens em um redirecionamento para o aplicativo de página única em execução em um navegador da web. Portanto, é um requisito importante para registrar um onde seu aplicativo pode receber tokens URI de redirecionamento. Verifique se o redirecionamento de que URI corresponde exatamente com o URI para seu aplicativo.

No [portal do Azure](https://go.microsoft.com/fwlink/?linkid=2083908), navegue até seu aplicativo registrado, na **autenticação** página do aplicativo, selecione o **Web** plataforma e insira o valor da URI de redirecionamento para seu aplicativo na **URI de redirecionamento** campo.

## <a name="enable-the-implicit-flow"></a>Habilitar o fluxo implícito

No mesmo **autenticação** página, em **configurações avançadas**, você deve habilitar também a **concessão implícita**. Se seu aplicativo está executando somente de entrada de usuários e obter tokens de ID, é suficiente permitir **tokens de ID** caixa de seleção.

Se seu aplicativo também precisa obter tokens de acesso para chamar APIs, certifique-se de habilitar o **tokens de acesso** caixa de seleção. Para obter mais informações, consulte [tokens de ID](./id-tokens.md) e [tokens de acesso](./access-tokens.md).

## <a name="api-permissions"></a>Permissões de API

Aplicativos de página única podem chamar APIs em nome do usuário conectado. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para acessar APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-spa-app-configuration.md)
