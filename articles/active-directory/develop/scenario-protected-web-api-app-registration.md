---
title: API da web - registro de aplicativo protegida | Azure
description: Saiba como criar uma API Web protegida e as informações necessárias registrar o aplicativo.
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
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074899"
---
# <a name="protected-web-api---app-registration"></a>API - registro do aplicativo web protegida

Este artigo explica as especificidades de registro de aplicativo para uma API web protegida.

Confira [Início Rápido: Registrar um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md) para obter as etapas comuns sobre como registrar o aplicativo.

## <a name="accepted-token-version"></a>Versão aceita de token

O ponto de extremidade de plataforma de identidade do Microsoft pode emitir a dois tipos de tokens: tokens da v1.0 e v2.0 tokens. Você pode aprender mais sobre esses tokens em [tokens de acesso](access-tokens.md). A versão de token aceita depende de **suporte para tipos de conta** você escolheu ao criar seu aplicativo:

- Se o valor de **suporte para tipos de conta** é **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, o Skype, Xbox, Outlook.com)**, a versão do token aceito será v 2.0.
- Caso contrário, a versão de token aceita será v 2.0.

Depois de criar o aplicativo, você pode alterar a versão de token aceita seguindo estas etapas:

1. No portal do Azure, selecione seu aplicativo e, em seguida, selecione a **manifesto** para seu aplicativo.
2. No manifesto, pesquise **"accessTokenAcceptedVersion"** e veja que seu valor é **2**. Essa propriedade permite que o Azure AD sabe que a API da web aceita tokens da v2.0. Se ele estiver **nulo**, a versão do token aceito será v1.0.
3. Clique em **Salvar**.

> [!NOTE]
> Cabe à API para decidir qual versão de token (v 1.0 ou 2.0) aceita da web. Quando os clientes solicitam um token para sua API da web usando o ponto de extremidade do Microsoft identity platform v 2.0, ele obterá um token que indica qual versão é aceito pela API da web.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

As APIs da Web não precisam registrar um URI de redirecionamento como nenhum usuário está conectado interativamente.

## <a name="expose-an-api"></a>Expor uma API

Outra configuração específica para APIs da web é a API exposta e os escopos expostos.

### <a name="resource-uri-and-scopes"></a>URI de recurso e escopos

Escopos são geralmente no formato `resourceURI/scopeName`. Para o Microsoft Graph, os escopos têm atalhos, como `User.Read`, mas essa cadeia de caracteres é apenas um atalho para `https://graph.microsoft.com/user.read`.

Durante o registro de aplicativo, você precisará definir os seguintes parâmetros:

- Um URI de recurso - por padrão, o portal de registro de aplicativo recomenda que você use `api://{clientId}`. Esse URI de recurso é exclusivo, mas não é humana legível. Você pode alterá-lo, mas certifique-se de que ele seja exclusivo.
- Um ou vários escopos

Os escopos também são exibidos na tela de consentimento é apresentada aos usuários finais que usam seu aplicativo. Portanto, você precisará fornecer as cadeias de caracteres correspondentes que descrevem o escopo:

- Como visto pelo usuário final
- Como visto pelo administrador do locatário, que poderá conceder consentimento do administrador

### <a name="how-to-expose-the-api"></a>Como expor a API

1. Selecione o **expor uma API** seção no registro do aplicativo, e:
   1. Selecione **Adicionar um escopo**.
   1. Aceitar a proposta URI da ID do aplicativo (api :// {clientId}), selecionando **salvar e continuar**.
   1. Insira os parâmetros s seguir:
      - Para **nome do escopo**, use `access_as_user`.
      - Para **quem pode consentir**, certifique-se a **administradores e usuários** opção está selecionada.
      - Na **nome de exibição de consentimento do administrador**, tipo `Access TodoListService as a user`.
      - Na **descrição de consentimento do administrador**, tipo `Accesses the TodoListService Web API as a user`.
      - Na **nome de exibição de consentimento do usuário**, tipo `Access TodoListService as a user`.
      - Na **descrição do consentimento do usuário**, tipo `Accesses the TodoListService Web API as a user`.
      - Manter **estado** definido como **habilitado**.
      - Selecione **adicionar escopo**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-protected-web-api-app-configuration.md)
