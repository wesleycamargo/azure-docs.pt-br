---
title: Configurar a inscrição e entrada com a conta do GitHub usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do GitHub em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88fffd28319101c112f848eebc6e8ee27f7f863e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952011"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do GitHub usando o Azure Active Directory B2C

> [!NOTE]
> Esse recurso está em visualização.
> 

Para usar uma conta do GitHub como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do GitHub, consiga uma conta em [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Crie um aplicativo GitHub OAuth

1. Entrar no site de [Desenvolvedor do GitHub](https://github.com/settings/developers) com suas credenciais do GitHub.
2. Escolha **Aplicativos do OAuth** e, em seguida, escolha **Registrar um novo aplicativo**.
3. Insira um **nome do Aplicativo** e **URL da home page**.
4. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` em **URL de retorno de chamada de autorização**. Substitua **{tenant}** pelo nome do locatário o seu Azure AD B2C (por exemplo, B2C.onmicrosoft.com).
5. Clique em **Registrar aplicativo**.
6. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar a conta do GitHub como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *Github*.
6. Escolha **Tipo de provedor de identidade**, marque **Github (versão prévia)** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID do Cliente que você registrou anteriormente como a **ID do Cliente** e insira o Segredo do Cliente que você registrou como o **Segredo do Cliente** do aplicativo de conta do Github criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração de conta do GitHub.