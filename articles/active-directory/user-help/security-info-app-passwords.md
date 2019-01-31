---
title: Configurar senhas de aplicativos usando informações de segurança - Azure Active Directory | Microsoft Docs
description: Configure senhas geradas automaticamente (senhas de aplicativo) para usar com cada aplicativo sem navegador, separado de uma senha normal, usando informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 185c6e18a84369702de3bd0c398a9cc0c64b8217
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191373"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Gerenciar senhas de aplicativos usando informações de segurança (visualização)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Determinados aplicativos que não são de navegador, como o Outlook 2010, não oferecem suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas, o aplicativo não funcionará. Para contornar este problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo que não seja o navegador, separado da sua senha normal.

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas de aplicativos são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos em sua área de trabalho.

    >[!Note]
    >Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, depois que a verificação em duas etapas estiver ativada, você não precisará mais de senhas de aplicativos para clientes do Office 2013. Para obter mais informações, consulte o artigo [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-and-delete-app-passwords-using-security-info"></a>Criar e excluir senhas de aplicativos usando informações de segurança

Se você usa a verificação em duas etapas com sua conta do trabalho ou da escola e o administrador ativou a experiência de informações de segurança, você pode criar e excluir suas senhas de aplicativos usando o portal Meus aplicativos.

Se o administrador não tiver ativado a experiência de informações de segurança, você deverá seguir as instruções e as informações na seção [Gerenciar senhas de aplicativo para verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md).

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Para criar senhas de aplicativos usando o portal Meus aplicativos

1. Entre sua conta corporativa ou de estudante.

2. Vá para myapps.microsoft.com, selecione seu nome no canto superior direito da página e selecione **Profile**.

3. No **Gerenciar conta** área, selecione **editar informações de segurança**.

    ![Tela de perfil com o link Editar informações de segurança realçado](media/security-info/security-info-profile.png)

4. Na tela **Manter sua conta segura**, selecione **Adicionar informações de segurança**.

    ![Tela de informações de segurança com informações editáveis existentes](media/security-info/security-info-edit-add-info.png)

5. Na tela **Adicionar informações de segurança**, selecione **Senha do aplicativo**.

6. Na tela **Criar sua senha do aplicativo**, digite um nome para a senha do seu aplicativo e, em seguida, selecione **Próximo**.

    ![Tela em que você nomeia a senha do seu aplicativo](media/security-info/security-info-name-app-password.png)

7. Selecione **Copiar** para copiar a senha para a área de transferência e, em seguida, selecione **Próximo**.

    ![Tela com a senha de aplicativo para a cópia](media/security-info/security-info-create-app-password.png)
    
8. Certifique-se de que a senha da aplicação aparece no ecrã **Manter a sua conta segura**.

    ![Manter a tela segura, com uma senha de aplicativo](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Para excluir senhas de aplicativos usando o portal Meus aplicativos

1. Na tela **Manter sua conta segura**, selecione **X** ao lado da senha do aplicativo a ser excluída.

    ![Mantenha a tela segura, exclua a senha do aplicativo](media/security-info/security-info-keep-secure-delete-app-password.png)

2. Na tela **Excluir senha do aplicativo**, selecione **Excluir**.

    ![Excluir a tela de senha de aplicativo](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Para mais informações gerais sobre informações de segurança e o que você pode fazer, consulte [Visão geral das informações de segurança](user-help-security-info-overview.md) 
