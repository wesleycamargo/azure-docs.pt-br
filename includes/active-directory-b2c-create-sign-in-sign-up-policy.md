---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455649"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Em **Gerenciar**, selecione **Fluxos dos usuários** e clique em +**Novo fluxo de usuário**.

![Selecionar novo fluxo de usuário](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Na guia **Recomendado**, selecione **Inscrever-se e entrar**.

![Selecionar o inscrever-se e entrar no fluxo de usuário](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Insira um **Nome** de fluxo de usuário para referência do seu aplicativo. Por exemplo, insira: `SiUpIn`.

Em **Provedores de identidade**, marque **Inscrição por email**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado.

Em **Autenticação multifator**, escolha **Habilitado** ou **Desabilitado**.

![Insira um nome e selecione Inscrição por email como provedor de identidade](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

Em **Declarações e atributos de usuário**, selecione **Mostrar mais** para ver a lista completa de atributos e declarações que podem ser escolhidos.

Na coluna **Coletar atributo**, escolha os atributos que você quer coletar do consumidor durante a inscrição. Por exemplo, marque **País/Região**, **Nome de Exibição** e **CEP**.

Na coluna **Declaração de retorno**, escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao aplicativo, após uma experiência de inscrição ou de entrada obtida com êxito. Por exemplo, selecione **Nome de Exibição**, **Provedor de Identidade**, **CEP**, **Novo Usuário** e **ID de Objeto do Usuário**.

Clique em **OK**.

![Selecione alguns atributos de usuário e as declarações e clique em botão OK](./media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Clique em **Criar** para adicionar o fluxo de usuário. O fluxo de usuário é listado como **B2C_1_SiUpIn**. O prefixo **B2C_1_** está anexado ao nome.

Selecione **Executar fluxo de usuário**. Verifique as configurações especificadas na tabela e, em seguida, clique em **Executar fluxo de usuário**.

![Selecionar Executar fluxo de usuário](./media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Configuração      | Value  |
| ------------ | ------ |
| **Aplicativo** | Aplicativo B2C da Contoso |
| **URL de Resposta** | `https://localhost:44316/` |

Uma nova guia do navegador é aberta e você poderá verificar a experiência de inscrição ou entrada do consumidor, conforme configurado.

> [!NOTE]
> Leva até um minuto para que a criação do fluxo de usuário e as atualizações entrem em vigor.
>

