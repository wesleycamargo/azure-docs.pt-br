---
title: Redefinir a senha de um usuário - Azure Active Directory | Microsoft Docs
description: Instruções sobre como redefinir a senha de um usuário usando o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce76fa7fecb8a82f76d40827bfa868061a0ed584
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204892"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Redefinir a senha de um usuário usando o Azure Active Directory
Como um administrador, você pode redefinir a senha de um usuário se a senha for esquecida, se o usuário for bloqueado em um dispositivo ou se o usuário nunca recebeu uma senha.

>[!Note]
>A menos que o locatário do Azure AD seja o diretório base de um usuário, você não poderá redefinir a senha. Isso significa que, se o usuário estiver conectado na organização usando uma conta de outra organização, uma conta Microsoft ou uma conta do Google, você não poderá redefinir a senha.<br><br>Se o usuário tiver uma fonte de autoridade, como Windows Server Active Directory, somente será possível redefinir a senha se você ativar o write-back de senha.<br><br>Se o usuário tiver uma fonte de autoridade como Azure AD Externo, não será possível redefinir a senha. Somente o usuário, ou um administrador no Azure AD Externo, pode redefinir a senha.

>[!Note]
>Se você não for um administrador e estiver procurando para obter instruções sobre como redefinir sua própria senha corporativa ou de estudante, consulte [Redefinir a senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Para redefinir uma senha

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global, administrador de usuários ou administrador de senhas. Para obter mais informações sobre as funções disponíveis, consulte [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Selecione **Azure Active Directory**, selecione **Usuários**, pesquise e selecione o usuário que precisa da redefinição e, em seguida, selecione **Redefinir Senha**.

    A página **Alain Charon - Perfil** aparece com a opção **Redefinir senha**.

    ![Página do perfil do usuário, com a opção Redefinir senha destacada](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na página **Redefinir senha**, selecione **Redefinir senha**.

    Uma senha temporária é gerada automaticamente para o usuário.

4. Copie a senha e entregue ao usuário. O usuário será solicitado a alterar a senha durante o próximo processo de entrada.

    >[!Note]
    >A senha temporária nunca expira. Na próxima vez em que o usuário entrar, a senha continuará funcionando, independentemente de quanto tempo se passou desde que a senha temporária foi gerada.

## <a name="next-steps"></a>Próximas etapas
Após redefinir a senha do usuário, você poderá executar os processos básicos a seguir:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções a usuários](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou poderá executar cenários de usuário mais complexos como atribuir delegados, usar políticas e compartilhar contas de usuários. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../users-groups-roles/index.yml).
