---
title: Aplicativo de Web que se conecta os usuários (mover para a produção) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que conecta os usuários (mover para a produção)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074704"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Aplicativo Web que assina usuários - passar para a produção

Agora que você sabe como adquirir um token para chamar APIs da web, saiba como movê-lo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

### <a name="calling-web-apis-scenario"></a>Cenário APIs da web chamada

Uma vez web entrar usuários de seu aplicativo, ele pode chamar APIs da web em nome dos usuários conectados. Chamar APIs da web do aplicativo web é o objeto do cenário a seguir:

> [!div class="nextstepaction"]
> [Aplicativo Web que chamadas às APIs da web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Mergulho profundo – tutorial do aplicativo web

Saiba mais sobre outras maneiras de entrar usuários com o tutorial do ASP.NET Core: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Este é um tutorial progressivo com código de produção pronto para um aplicativo de web, incluindo como adicionar a entrada.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
