---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114694"
---
Para habilitar a redefinição de senha refinada no aplicativo, você utiliza um fluxo de usuário de **redefinição de senha**. Observe que a opção de redefinição de senha de todos os locatários é especificada [aqui](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Este e fluxo de usuário descreve as experiências pelas quais os clientes passarão durante a redefinição de senha e o conteúdo dos tokens que o aplicativo receberá após a conclusão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Em **Gerenciar**, selecione **Fluxos dos usuários** e clique em +**Novo fluxo de usuário**.

![Selecionar novo fluxo de usuário](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Na guia **Recomendado**, clique em **Redefinição de senha**.

Insira um **Nome** de fluxo de usuário para referência do seu aplicativo. Por exemplo, insira: `SSPR`.

Em **Provedores de identidade**, marque **Redefinir senha usando endereço de email**.

![Inserir nome e selecionar redefinir senha usando endereço de email como um provedor de identidade](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Em **Declarações do aplicativo**, clique em **Mostrar mais** e escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao seu aplicativo após uma experiência de redefinição de senha com êxito. Por exemplo, selecione **ID de Objeto do Usuário**.

Clique em **OK**.

![Selecione algumas declarações de aplicativo e clique no botão OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Clique em **Criar** para adicionar o fluxo de usuário. O fluxo de usuário é listado como **B2C_1_SSPR**. O prefixo **B2C_1_** está anexado ao nome.

Clique em **Executar fluxo de usuário**. Verifique as configurações especificadas na tabela e, em seguida, clique em **Executar fluxo de usuário**.

![Selecionar e executar o fluxo de usuário](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Configuração      | Valor  |
| ------------ | ------ |
| **Aplicativo** | Aplicativo B2C da Contoso |
| **Selecionar URL de resposta** | `https://localhost:44316/` |

Uma nova guia do navegador é aberta e você pode verificar a experiência de redefinição de senha do consumidor em seu aplicativo.

> [!NOTE]
> Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
>
