---
title: Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do QQ em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11bb5bf132103bed9e154a12c0e628177ca6a57a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344917"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C

> [!NOTE]
> Esse recurso está em visualização.
> 

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar uma conta do QQ como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do QQ, obtenha uma conta em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrar-se no programa de desenvolvedores do QQ

1. Entre no [portal do desenvolvedor do QQ](http://open.qq.com) com suas credenciais de conta do QQ.
2. Após a autenticação, acesse [http://open.qq.com/reg](http://open.qq.com/reg) para registrar-se como desenvolvedor.
3. Selecione **个人** (desenvolvedor individual).
4. Insira as informações necessárias e selecione **下一步** (próxima etapa).
5. Conclua o processo de verificação de email. Você precisará aguardar alguns dias para ser aprovado depois de registrar-se como desenvolvedor. 

### <a name="register-a-qq-application"></a>Registrar um aplicativo do QQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Selecione **应用管理**(gerenciamento de aplicativos).
5. Selecione **创建应用** (criar aplicativo) e insira as informações necessárias.
7. Insira `https://{tenant_name}.b2clogin.com/te/{tenant_name}.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno de chamada). Por exemplo, se `tenant_name` for contoso, defina a URL como `https://contoso.b2clogin.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Selecione **创建应用** (criar aplicativo).
9. Na página de confirmação, selecione **应用管理**(gerenciamento de aplicativos) para retornar à página de gerenciamento de aplicativos.
10. Selecione **查看** (exibir) ao lado do aplicativo que você criou.
11. Selecione **修改** (editar).
12. Copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisa de ambos os valores para adicionar o provedor de identidade para seu locatário.

## <a name="configure-qq-as-an-identity-provider"></a>Configurar o QQ como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-qq-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *QQ*.
6. Escolha **Tipo de provedor de identidade**, selecione **QQ (versão prévia)** e clique em **OK**.
7. Selecione **Configurar este provedor de identidade**. Insira a ID do aplicativo que você anotou anteriormente como a **ID do cliente** e insira a chave de aplicativo que você registrou como o **Segredo do cliente** do aplicativo do QQ que criou anteriormente.
8. Clique em **OK** e em **Criar** para salvar sua configuração do QQ.

