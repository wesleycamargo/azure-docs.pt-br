---
title: Aplicativo Web que chamadas de web APIs (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que chama APIs (registro de aplicativo) de web
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075184"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Aplicativo Web que chama APIs - registro do aplicativo de web

Uma Web aplicativo chamar APIs da web tem o mesmo registro como usuários de entrar em um aplicativo Web. Você precisará, portanto, siga as instruções em [que usuários entrar - registro de aplicativo do aplicativo Web](scenario-web-app-sign-user-app-registration.md)

No entanto desde o aplicativo Web agora chamadas de web APIs, torna-se um aplicativo cliente confidencial. É por isso que há um pouco de registro extra necessário: ele precisa compartilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Aplicativos da Web chamam APIs em nome do usuário conectado. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para acessar APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-app-call-api-app-configuration.md)
