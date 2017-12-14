---
title: Redefinir senhas no Azure Active Directory | Microsoft Docs
description: "O administrador iniciou a redefinição de senha para um usuário no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: 6d01dff567e49b602e98f717dace4dc75abecb4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Redefinir a senha de um usuário no Azure Active Directory

Os administradores podem precisar redefinir a senha do usuário em casos em que as esqueceram, foram bloqueados ou outros cenários. As etapas a seguir orientam você na redefinição de senha do usuário.

## <a name="how-to-reset-the-password-for-a-user"></a>Como redefinir a senha de um usuário

1. Entre no [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que tenha permissões de diretório para redefinir senhas de usuários.
2. Selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione o usuário para o qual deseja redefinir a senha.
2. Para o usuário selecionado, escolha **Redefinir senha**.

    ![Redefinir uma senha para um usuário do Perfil do usuário no Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Em **Redefinir senha**, selecione **Redefinir senha**.
7. Uma senha temporária é exibida que você pode então fornecer ao usuário. Será solicitado que o usuário altere sua senha na próxima vez em que fizer logon. 

   > [!NOTE]
   > Esta senha temporária não tem um tempo de expiração, portanto será válida até que o usuário faça logon e seja então obrigado a alterá-la. 

## <a name="next-steps"></a>Próximas etapas
* [Adicionar um usuário](active-directory-users-create-azure-portal.md)
* [Atribuir funções de administrador a um usuário](active-directory-users-assign-role-azure-portal.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Excluir um usuário do Azure AD](active-directory-users-delete-user-azure-portal.md)
