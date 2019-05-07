---
title: Web API que chamadas às APIs da web (mover para a produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma web API que chamadas downstream web APIs (mover para a produção).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074749"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API da Web que chama APIs - web passar para a produção

Depois que você já adquiriu um token para chamar APIs da web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que você conhece as Noções básicas de como chamar as APIs web de sua própria API da web, você pode estar interessado é este tutorial, que descreve o código que foi usado para criar uma API web protegida chamar APIs da web.

| Amostra | Plataforma | DESCRIÇÃO |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2.2 API Web ASP.NET Core, área de trabalho (WPF) | API da Web do ASP.NET Core 2.2 chamando o Microsoft Graph, em si chamado a partir de um aplicativo WPF usando a plataforma de identidade da Microsoft (v2.0) |
