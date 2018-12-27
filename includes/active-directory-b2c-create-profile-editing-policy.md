---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742294"
---
Se quiser habilitar a edição de perfil no aplicativo, use um fluxo de usuário de **edição de perfil**. Esse luxo de usuário descreve as experiências pelas quais os clientes passarão durante a edição de perfil e o conteúdo dos tokens que o aplicativo receberá após a conclusão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Em **Gerenciar**, selecione **Fluxos dos usuários** e clique em +**Novo fluxo de usuário**.

![Selecionar novo fluxo de usuário](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Na guia **Recomendado**, clique em **Edição de perfil**.

Insira um **Nome** de fluxo de usuário para referência do seu aplicativo. Por exemplo, insira: `SiPe`.

Clique em **Provedores de identidade** e marque **Conectar à Conta Local**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado.

![Selecione Inscrição na Conta Local como provedor de identidade e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

Em **Atributos de usuário**, clique em **Mostrar mais**. Na coluna **Coletar atributo**, escolha os atributos que o consumidor pode exibir e editar no perfil dele. Por exemplo, marque **País/Região**, **Nome de Exibição** e **CEP**.

Na coluna **Retornar declaração**, escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao aplicativo após uma experiência de edição de perfil com êxito. Por exemplo, selecione **Nome de Exibição** e **CEP**.

Clique em **OK**.

![Selecione algumas declarações de aplicativo e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Clique em **Criar** para adicionar o fluxo de usuário. O fluxo de usuário é listado como **B2C_1_SiPe**. O prefixo **B2C_1_** está anexado ao nome.

Selecione **Executar fluxo de usuário**. Verifique as configurações especificadas na tabela e, em seguida, clique em **Executar fluxo de usuário**.

![Selecionar e executar o fluxo de usuário](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Configuração      | Valor  |
| ------------ | ------ |
| **Aplicativo** | Aplicativo B2C da Contoso |
| **URL de Resposta** | `https://localhost:44316/` |

Uma nova guia do navegador é aberta e você poderá verificar a experiência de edição de perfil do consumidor, conforme configurado.

> [!NOTE]
> Leva até um minuto para que a criação do fluxo de usuário e as atualizações entrem em vigor.
>