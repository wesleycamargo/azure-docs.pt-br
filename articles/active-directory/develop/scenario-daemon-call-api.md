---
title: O daemon aplicativo chamar APIs da web (chamar APIs da web) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chamadas de web APIs (chamar APIs da web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076264"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplicativo daemon que chamadas de web APIs - chamar uma API da web do aplicativo

Um aplicativo daemon pode chamar uma API da web de um aplicativo de daemon do .NET ou chamar várias APIs de web previamente aprovada.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Chamando uma API da web em um aplicativo de daemon do .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Chamar várias APIs

Para aplicativos de daemon, que você chamar APIs da web precisam ser aprovadas previamente. Não haverá qualquer consentimento incremental com aplicativos daemon (não há nenhuma interação do usuário). O administrador de locatário precisa previamente consentir o aplicativo e todas as permissões de API. Se você deseja chamar várias APIs, você precisará adquirir um token para cada recurso, cada chamada de tempo `AcquireTokenForClient`. MSAL usará o cache de token de aplicativo para evitar chamadas de serviço desnecessário.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo daemon - mover para a produção](./scenario-daemon-production.md)