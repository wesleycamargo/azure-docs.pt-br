---
title: Como gerenciar senhas de aplicativos no Active Directory do Azure | Microsoft Docs
description: Esta página ajudará os usuários a entenderem quais são as senhas do aplicativo e o que elas são usadas com relação à verificação em duas etapas.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 836f426be950e33031ff74276218d1ba59f1f2f7
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300906"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerenciar senhas de aplicativos para verificação em duas etapas

Determinados aplicativos que não são de navegador, como o Outlook 2010, não oferecem suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas, o aplicativo não funcionará. Para contornar este problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo que não seja o navegador, separado da sua senha normal.

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas de aplicativos são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos em sua área de trabalho.

    >[!Note]
    >Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, depois que a verificação em duas etapas estiver ativada, você não precisará mais de senhas de aplicativos para clientes do Office 2013. Para obter mais informações, consulte o artigo [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e excluir suas senhas de aplicativo

Você recebe uma senha do aplicativo durante o registro inicial da verificação em duas etapas. Se você precisar de mais do que uma senha, poderá criar senhas adicionais com base em como você usa a verificação em duas etapas:

- **Você usa a verificação em duas etapas com sua conta do trabalho ou da escola e o portal MyApps.** Crie e exclua suas senhas de aplicativos usando as instruções em [Criar e excluir senhas de aplicativos usando a seção MyApps do portal](#create-and-delete-app-passwords-using-the-myapps-portal) deste artigo. Para obter mais informações sobre o portal MyApps e como usá-lo, consulte [O que é o portal MyApps no Active Directory do Azure?](active-directory-saas-access-panel-introduction.md).

- **Você usa a verificação em duas etapas com sua conta do trabalho ou da escola e o portal do Office 365.** Crie e exclua suas senhas de aplicativos usando as instruções em [Criar e excluir senhas de aplicativos usando a seção do portal do Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) deste artigo.

- **Você usa a verificação em duas etapas com sua conta pessoal da Microsoft.** Crie e exclua suas senhas de aplicativos usando a página [Noções básicas de segurança](https://account.microsoft.com/account/) com sua conta pessoal da Microsoft. Para mais informações, consulte o artigo [Senhas do aplicativo e verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification).

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Crie e exclua senhas de aplicativos usando o portal MyApps
Você pode criar e excluir senhas de aplicativos através do portal MyApps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma senha de aplicativo usando o portal do MyApps

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Selecione seu nome no canto superior direito e, em seguida, escolha **perfil**.

3. Escolha **Verificação de Segurança Adicional**.

   ![Selecione Verificação de Segurança Adicional – captura de tela](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Selecione **Senhas de aplicativo**.

   ![Selecione senhas de aplicativo – captura de tela](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Clique em **Criar**.

6. Digite um nome para a senha do aplicativo e selecione **Próximo**.

7. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.
   
    ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para excluir uma senha de aplicativo usando o portal do MyApps

1. Vá para o seu perfil e selecione **Verificação Adicional de Segurança**.

2. Selecione **Senhas do aplicativo** e, em seguida, selecione **Excluir** ao lado da senha do aplicativo que você deseja excluir.

   ![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Selecione **Sim** para confirmar que deseja excluir a senha e, em seguida, selecione **Fechar**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e excluir senhas de aplicativos usando o portal do Office 365

Se você usa a verificação em duas etapas com sua conta do trabalho ou da escola e seus aplicativos do Office 365, pode criar e excluir suas senhas de aplicativos usando o portal do Office 365. Você pode ter no máximo 40 senhas de aplicativos a qualquer momento. Se você precisar de outra senha do aplicativo depois desse limite, terá que excluir uma das suas senhas de aplicativo existentes.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar senhas de aplicativos usando o portal do Office 365

1. Entre sua conta corporativa ou de estudante.

2. Vá para https://portal.office.com, selecione o ícone **Configurações** no canto superior direito da página **Portal do Office 365** e expanda **Verificação de segurança adicional**.

    ![Do portal mostrando Office expandido a área de verificação de segurança adicional](media/security-info/security-info-o365password.png)

3. Selecione o texto que diz: **Crie e gerencie senhas de aplicativos** para abrir a página **senhas de aplicativos**.

4. Selecione **Criar**, digite um nome amigável para o aplicativo que precisa da senha de aplicativo e, em seguida, selecione **próxima**.

5. Selecione **Copiar senha para a área de transferência** e, em seguida, selecione **Fechar**.

6. Use a senha do aplicativo copiada para fazer login no aplicativo que não é do navegador. Você só precisa digitar essa senha uma vez e é lembrado para o futuro.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para excluir senhas de aplicativo usando o portal do Office 365

1. Entre sua conta corporativa ou de estudante.

2. Vá para https://portal.office.com, selecione o ícone **Configurações** no canto superior direito da página **Portal do Office 365** e, em seguida, selecione **Verificação adicional de segurança**.

3. Selecione o texto que diz: **Crie e gerencie senhas de aplicativos** para abrir a página **senhas de aplicativos**.

4. Selecione **Excluir** para excluir a senha do aplicativo, selecione **Sim** na caixa de confirmação e, em seguida, selecione **Fechar**.

    A senha do aplicativo é excluída com sucesso.

5. Siga as etapas para criar uma senha de aplicativo para criar sua nova senha de aplicativo.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as senhas do aplicativo não estiverem funcionando corretamente

Certifique-se de ter digitado sua senha corretamente. Se tiver certeza de que você digitou sua senha corretamente, tente entrar novamente e criar uma nova senha do aplicativo. Se nenhuma dessas opções resolver seu problema, entre em contato com o suporte da sua empresa para que ele possa excluir suas senhas de aplicativo existentes, permitindo que você crie novas. 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicativo Autenticador Microsoft](microsoft-authenticator-app-how-to.md) para verificar suas conexões com notificações de aplicativo, em vez de receber mensagens ou ligações.
