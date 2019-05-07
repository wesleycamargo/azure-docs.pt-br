---
title: O daemon aplicativo chamar APIs da web (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama as APIs da web
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075874"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicativo daemon que chamadas às APIs da web

Aprenda tudo o que você precisa para compilar um aplicativo daemon que chama as APIs da web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Visão geral

Seu aplicativo pode adquirir um token para chamar uma API da web em nome próprio (não em nome do usuário). Esse cenário é útil para aplicativos de daemon. Ele está usando o OAuth 2.0 padrão [as credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) conceder.

![Aplicativos Daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicativos daemon:

- Aplicativos da Web que são usados para provisionar ou administrar os usuários ou processos em um diretório do lote
- Aplicativos da área de trabalho, como (serviços do windows no Windows) ou processos de daemons no Linux que executam trabalhos em lotes ou um serviço de sistema operacional em execução em segundo plano
- APIs da Web que precise manipular diretórios, os usuários não específicos

Há outro caso comum em que aplicativos não-daemon usam as credenciais do cliente: mesmo quando eles atuam em nome dos usuários, eles precisam acessar uma API da web ou um recurso com sua identidade por motivos técnicos. Um exemplo é o acesso aos segredos no cofre de chaves ou um banco de dados SQL do Azure para um cache.

Aplicativos que adquirem um token para suas próprias identidades:

- São aplicativos cliente confidencial. Esses aplicativos, considerando que acessarem recursos independentemente de um usuário precisam provar sua identidade. Eles também são aplicativos confidenciais em vez disso, o que eles precisam ser aprovadas por administradores de locatários do Azure Active Directory (Azure AD).
- Ter registrado um segredo (senha de aplicativo ou certificado) com o Azure AD. Esse segredo é passado durante a chamada para o Azure AD para obter um token.

## <a name="specifics"></a>Informações específicas

> [!IMPORTANT]
>
> - Interação do usuário não é possível com um aplicativo daemon. Um aplicativo daemon requer sua própria identidade. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID do aplicativo, credenciais (senha ou certificado) e aplicativo URI da ID do Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso (e um token de atualização) do ponto de extremidade de plataforma Microsoft identity, que é usada para chamar a API web (e é atualizada conforme necessário).
> - Como a interação do usuário não for possível, consentimento incremental não será possível. Todas as permissões de API necessárias precisam ser configurados no momento do registro do aplicativo e o código do aplicativo apenas solicita permissões definidas estaticamente. Isso também significa que aplicativos daemon não dará suporte ao consentimento incremental.

Para desenvolvedores, a experiência de ponta a ponta para este cenário tem os seguintes aspectos:

- Aplicativos daemon só podem funcionar em locatários do Azure AD. Não faria sentido criar um aplicativo daemon que tenta manipular contas pessoais da Microsoft. Se você for um desenvolvedor de aplicativo do linha de negócios (LOB), você criará seu aplicativo de daemon em seu locatário. Se você for um ISV, você talvez queira criar um aplicativo de daemon de vários locatários. Ele precisará ser consentida por cada administrador de locatário.
- Durante o [registro de aplicativo](./scenario-daemon-app-registration.md), o **URI de resposta** não é necessária. Você precisa compartilhar segredos ou certificados com o Azure AD e você precisará solicitar permissões de aplicativos e conceder consentimento do administrador para usar essas permissões de aplicativo.
- O [configuração de aplicativo](./scenario-daemon-app-configuration.md) precisará fornecer as credenciais de cliente como compartilhado com o Azure AD durante o registro de aplicativo.
- O [escopo](scenario-daemon-acquire-token.md#scopes-to-request) usado para adquirir um token com as credenciais do cliente fluxo precisa ser um escopo estático.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo daemon - registro de aplicativo](./scenario-daemon-app-registration.md)
