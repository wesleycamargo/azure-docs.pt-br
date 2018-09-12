---
title: Configurar a inscrição e entrada com a conta do Facebook usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Facebook em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344599"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Facebook usando o Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do Facebook, consiga uma conta em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
2. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para fazer isso, escolha **Registrar**, no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
3. Escolha **Meus Aplicativos** e clique em **Adicionar um Novo Aplicativo**. 
4. Insira um **Nome de Exibição** e um **Email de Contato** válido.
5. Clique em **Criar ID de Aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
6. Escolha **Configurações** > **Básicas**.
7. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
8. Insira `https://{tenantname}.b2clogin.com/` em **URL do Site**. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com`.
9. Selecione **Salvar alterações**.
11. Na parte superior da página, copie o valor de **ID do Aplicativo**. 
12. Clique em **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
13. Escolha **Produtos** e, em seguida, escolha **Configurar** em **Logon do Facebook**.
14. Escolha **Configurações** em **Logon do Facebook**.
15. Insira `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` em **URIs de Redirecionamento do OAuth Válidas**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c). Clique em **Salvar Alterações** na parte inferior da página.
16. Para disponibilizar seu aplicativo do Facebook para o Azure AD B2C, escolha **Revisão de Aplicativos**, defina **Tornar meu aplicativo público?** como **SIM**, escolha uma categoria, por exemplo `Business and Pages`, e, em seguida, clique em **Confirmar**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Insira um **Nome**. Por exemplo, insira *Facebook*.
6. Escolha **Tipo de provedor de identidade**, marque **Facebook** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID do Aplicativo que você registrou anteriormente como a **ID do Cliente** e insira o Segredo do Aplicativo que você registrou como o **Segredo do Cliente** do aplicativo de conta do Facebook criado anteriormente).
8. Clique em **OK** e em **Criar** para salvar sua configuração do Facebook.