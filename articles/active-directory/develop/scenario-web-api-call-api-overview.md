---
title: API da Web que chama downstream web APIs (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar uma API que chamadas downstream APIs da web (visão geral) da web.
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075394"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Cenário: API da Web que chama as APIs da web

Aprenda tudo o que você precisa para compilar uma API web que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

Nesse cenário, a API web protegida que chamadas de web APIs, se baseia no cenário de "Proteger uma API da web". Para saber mais sobre esse cenário fundamental, consulte [API Web protegida - cenário](scenario-protected-web-api-overview.md) primeiro.

## <a name="overview"></a>Visão geral

- Um cliente (web, área de trabalho, aplicativo móvel ou de página única) - não representado no diagrama a seguir - chama uma API web protegida e fornece um token de portador JWT no cabeçalho Http "Autorização".
- A API web protegida valida o token e usa a MSAL `AcquireTokenOnBehalfOf` método solicitar (do Azure AD) outro token para que ele pode, em si, chamar uma segunda API da web (chamado a API web downstream) em nome do usuário.
- A API web protegida usa esse token para chamar uma API downstream. Ele também pode chamar `AcquireTokenSilent`posteriormente para solicitar tokens para outras APIs de downstream (mas ainda em nome do usuário mesmo). `AcquireTokenSilent` atualiza o token quando necessário.

![Chamar uma API web de API da Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Informações específicas

A parte de registro de aplicativo relacionado às permissões de API é clássica. A configuração do aplicativo envolve o uso de fluxo OAuth 2.0 on-behalf-of para trocar o token de portador JWT em relação a um token para a API downstream. Esse token é adicionado ao cache de token, onde ele está disponível nos controladores da API web e pode adquirir um token silenciosamente para chamar as APIs de downstream.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-web-api-call-api-app-registration.md)
