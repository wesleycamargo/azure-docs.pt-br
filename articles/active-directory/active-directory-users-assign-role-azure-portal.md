---
title: Atribuir um usuário às funções de administrador no Azure Active Directory | Microsoft Docs
description: Explica como alterar informações administrativas de usuário no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Atribuir um usuário às funções de administrador no Azure Active Directory
Este artigo explica como atribuir uma função administrativa a um usuário no Azure AD (Azure Active Directory). Para obter informações sobre como adicionar novos usuários em sua organização, consulte [Adicionar novos usuários ao Azure Active Directory](active-directory-users-create-azure-portal.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário
1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Usuários e grupos**.

   ![Abrir o gerenciamento de usuários](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Selecione **Todos os usuários**.
  
  ![Abrindo o grupo Todos os usuários](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Selecione um usuário na lista.
5. Para o usuário selecionado, selecione **Função do diretório**, em seguida, atribua o usuário a uma função na lista **Função do diretório**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Atribuindo um usuário a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* [Início rápido: Adicionar ou excluir usuários no Azure Active Directory](add-users-azure-active-directory.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Adicionar usuários convidados de outro diretório](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurar um usuário excluído](active-directory-users-restore.md)
