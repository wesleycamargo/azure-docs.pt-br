---
title: Protegido de API Web - visão geral | Azure
description: Saiba como compilar uma API (visão geral) de web protegido.
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074884"
---
# <a name="scenario-protected-web-api"></a>Cenário: API web protegida

Nesse cenário, vamos mostrar como você pode expor uma API da web e como você pode protegê-lo para que somente usuários autenticados pode acessar a API. Você vai querer permitir que usuários autenticados com o trabalho e contas de estudante ou pessoas contas pessoais da Microsoft usar sua API da web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Informações específicas

Aqui estão algumas informações específicas que você precisa saber para proteger as APIs da web:

- O registro do aplicativo deverá expor pelo menos um escopo. A versão do token aceito pela sua API da web depende do público-alvo de entrada.
- A configuração do código para a API da web deve validar o token que é usado ao chamar a API da web.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-protected-web-api-app-registration.md)
