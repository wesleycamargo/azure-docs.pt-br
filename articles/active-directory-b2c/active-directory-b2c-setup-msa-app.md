---
title: Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça inscrição e entrada aos consumidores com contas da Microsoft em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 338c2a197cb50091c3b272e0ce590341ffda1d7f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341076"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar uma conta da Microsoft como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta da Microsoft, consiga uma conta em [https://www.live.com/](https://www.live.com/).

1. Entre no [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) com suas credenciais da conta da Microsoft.
2. No canto superior direito, escolha **Adicionar um aplicativo**.
3. Forneça um **Nome** para seu aplicativo e clique em **Criar**.
4. Na página de registro, copie o valor da **Id do aplicativo**. Use-o para configurar sua conta da Microsoft como um provedor de identidade em seu locatário.
5. Escolha **Adicionar plataforma** e, em seguida, escolha **Web**.
6. Insira `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento**. Substitua **{tenant}** com o nome do locatário (por exemplo, contosob2c).
7. Escolha **Gerar Nova Senha** em **Segredos do Aplicativo**. Copie a nova senha exibida na tela. Você precisa dele para configurar uma conta da Microsoft como um provedor de identidade em seu locatário. A senha é uma credencial de segurança importante.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *MSA*.
6. Escolha **Tipo do provedor de identidade**, marque **Conta da Microsoft** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID do Aplicativo que você registrou anteriormente como a **ID do Cliente** e insira a senha que você registrou como o **Segredo do Cliente** do aplicativo de conta da Microsoft criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração da conta da Microsoft.

