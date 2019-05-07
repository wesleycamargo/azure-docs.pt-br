---
title: O daemon aplicativo chamar APIs da web (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama as APIs da web - registro de aplicativo
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076234"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplicativo daemon que chamadas às APIs - registro de aplicativo da web

Para um aplicativo daemon, aqui está o que você precisa saber ao registrar o aplicativo.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Considerando que aplicativos daemon só fazem sentido no locatário do Azure AD, quando você cria o aplicativo, você precisará escolher:

- qualquer um dos **contas neste diretório organizacional apenas**. Essa opção é o caso mais comum, como aplicativos daemon geralmente são escritos por desenvolvedores do linha de negócios (LOB).
- ou **contas em qualquer diretório organizacional**. Você fará essa opção se você for um ISV fornecer uma ferramenta de utilitário para seus clientes. Você precisará de administradores de locatários do cliente para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Nenhum URI de resposta de autenticação - necessária

No caso em que usa seu aplicativo cliente confidencial **apenas** as credenciais do cliente de fluxo, a URL de resposta não precisa ser registrado. Ele não é necessário para a criação/configuração do aplicativo. O fluxo de credenciais de cliente não usá-lo.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões de API - aplicativo permissões e consentimento do administrador

Um aplicativo daemon só pode solicitar permissões de aplicativo para APIs (permissões delegadas não). No **permissão de API** página de registro do aplicativo, depois que você selecionou **adicionar uma permissão** e escolhido a família de API, escolha **permissões de aplicativo**, e, em seguida, selecione suas permissões

![Aplicativo permissões e consentimento do administrador](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Aplicativos daemon exigem têm um administrador de locatários previamente consentir o aplicativo chamar a API da web. Esse consentimento é fornecido no mesmo **permissão de API** na página, selecionando um locatário admin **conceder consentimento do administrador para *nossa organização***

Se você for um ISV criando um aplicativo multilocatário, convém verificar os [implantação – caso de aplicativos multilocatário daemon](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) parágrafo.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo daemon - configuração de código do aplicativo](./scenario-daemon-app-configuration.md)
