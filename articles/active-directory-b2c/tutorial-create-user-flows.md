---
title: Tutorial – Criar fluxos dos usuários – Azure Active Directory B2C | Microsoft Docs
description: Saiba como criar fluxos dos usuários para seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e12b3cbcb8f7a433b37c450c84bd34745f68a22d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711513"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos dos usuários no Azure Active Directory B2C

Nos seus aplicativos, você pode ter [fluxos dos usuários](active-directory-b2c-reference-policies.md) que permitem que os usuários inscrevam-se, conectem-se ou gerenciem seu perfil. É possível criar vários fluxos dos usuários de diferentes tipos no locatário do Azure AD (Azure Active Directory) B2C e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Este tutorial mostra como criar alguns fluxos dos usuários recomendados usando o portal do Azure. Se você estiver procurando informações sobre como configurar um fluxo de ROPC (credenciais de senha de proprietário do recurso) em seu aplicativo, confira [Configurar o fluxo de credenciais de senha de proprietário do recurso no Azure AD B2C](configure-ropc.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registre seus aplicativos](tutorial-register-applications.md) que fazem parte dos fluxos dos usuários que deseja criar. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e de entrada

O fluxo do usuário de inscrição e credenciais controla as experiências de inscrição e credenciais do consumidor com uma única configuração. Os usuários do aplicativo são conduzidos pelo caminho certo, de acordo com o contexto.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.

    ![Alternar para o diretório de assinatura](./media/tutorial-create-user-flows/switch-directories.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. No menu da esquerda, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.

    ![Selecionar novo fluxo de usuário](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Selecione o fluxo do usuário **Inscrever-se e conectar-se** na guia Recomendado.

    ![Selecionar o fluxo do usuário de inscrição e conexão](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
7. Para **Provedores de identidade**, selecione **Inscrição por email**.

    ![Definir as propriedades do fluxo](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Para **Atributos e declarações do usuário**, escolha as declarações e atributos que deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais** e, depois, escolha **País/região**, **Nome de exibição** e **Código postal**. Clique em **OK**.

    ![Selecionar atributos e declarações](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Na página Visão geral do fluxo do usuário que você criou, selecione **Executar o fluxo do usuário**.
2. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo do usuário**e, em seguida, selecione **Inscreva-se agora**.

    ![Executar o fluxo do usuário](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Insira um endereço de email válido, clique em **Enviar código de verificação** e, em seguida, insira o código de verificação que você receber.
5. Insira uma nova senha e confirme-a.
6. Insira o nome que você deseja que seja exibido, selecione seu país e região, insira um código postal e, em seguida, clique em **Criar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
7. Agora, você pode executar o fluxo do usuário novamente e poderá se conectar com a conta que você criou. O token retornado inclui as declarações que você selecionou de nome, país e código postal.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Se quiser permitir que os usuários editem seu perfil no aplicativo, use um fluxo do usuário de edição de perfil.

1. No menu da esquerda, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
2. Selecione o fluxo do usuário **Edição de perfil** na guia Recomendado.
3. Insira um **Nome** para o fluxo de usuário. Por exemplo, *profileediting1*.
4. Para **Provedores de identidade**, selecione **Conexão na Conta Local**.
5. Para **Atributos do usuário**, escolha os atributos que deseja que o cliente seja capaz de editar no perfil dele. Por exemplo, selecione **Mostrar mais** e, depois, escolha **Nome de exibição** e **Cargo**. Clique em **OK**.
6. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Na página Visão geral do fluxo do usuário que você criou, selecione **Executar o fluxo do usuário**.
2. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo do usuário** e, em seguida, entre com a conta que criou anteriormente.
4. Agora, você tem a oportunidade de alterar o nome de exibição e o cargo do usuário. Clique em **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

É possível permitir que o usuário do seu aplicativo redefina a senha se necessário. Para habilitar a redefinição de senha, você deve usar um fluxo do usuário de redefinição de senha.

1. No menu da esquerda, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
2. Selecione o fluxo do usuário **Redefinição de senha** na guia Recomendado.
3. Insira um **Nome** para o fluxo de usuário. Por exemplo, *passwordreset1*.
4. Em **Provedores de identidade**, habilite **Redefinir senha usando endereço de email**.
5. Em Declarações do aplicativo, clique em **Mostrar mais** e escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao seu aplicativo. Por exemplo, selecione **ID de Objeto do Usuário**.
6. Clique em **OK**.
7. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Na página Visão geral do fluxo do usuário que você criou, selecione **Executar o fluxo do usuário**.
2. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo do usuário** e, em seguida, entre com a conta que criou anteriormente.
4. Agora, você pode alterar a senha para o usuário. Clique em **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

> [!div class="nextstepaction"]
> [Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)