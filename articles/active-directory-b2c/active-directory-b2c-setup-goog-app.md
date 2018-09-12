---
title: Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 477bd6047da639dcf21592a7ec0c1b80844e031e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337727"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do Google, consiga uma conta em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
2. Selecione **Criar projeto** e clique em **Criar**. Se você tiver criado projetos antes, selecione a lista de projetos e, em seguida, selecione **Novo Projeto**.
3. Insira um **Nome de projeto** e clique em **Criar**.
3. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
4. Selecione **Configurar tela de consentimento**.
5. Selecione ou especifique um **Endereço de email** válido, forneça um **Nome de produto mostrado para os usuários** e clique em **Salvar**.
6. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
7. Insira um **Nome** para seu aplicativo, insira `https://{tenant}.b2clogin.com` em **Origens de JavaScript autorizadas** e `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c).
8. Clique em **Criar**.
9. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar a conta do Google como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Insira um **Nome**. Por exemplo, insira *Google*.
6. Selecione **Tipo de provedor de identidade**, selecione **Google** e clique em **OK**.
7. Selecione **Configurar este provedor de identidade**. Insira a ID do cliente que você anotou anteriormente como a **ID do cliente** e insira o segredo do cliente que você registrou como o **Segredo do cliente** do aplicativo do Google que criou anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração do Google.

