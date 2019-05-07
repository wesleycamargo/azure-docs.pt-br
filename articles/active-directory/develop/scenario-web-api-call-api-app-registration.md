---
title: Web API que chamadas downstream APIs da web (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar uma API que chamadas downstream APIs da web (registro de aplicativo) da web
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075379"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Que chamadas de web APIs - registro do aplicativo de API da Web

Uma API da web que chama as APIs da web downstream tem o mesmo registro como uma API web protegida. Portanto, será necessário seguir as instruções em [API Web protegida - registro do aplicativo](scenario-protected-web-api-app-registration.md).

No entanto, desde que o aplicativo web agora chamadas de web APIs, torna-se um aplicativo cliente confidencial. É por isso que há informações de registro extra necessário: o aplicativo precisa compartilhar segredos (credenciais de cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Aplicativos da Web chamam APIs em nome do usuário para quem o token de portador foi recebido. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para acessar APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-api-call-api-app-configuration.md)
