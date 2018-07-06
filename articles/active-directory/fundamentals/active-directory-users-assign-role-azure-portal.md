---
title: Atribuir um usuário às funções de administrador no Azure Active Directory | Microsoft Docs
description: Como alterar informações administrativas de usuário no Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: ec30f1507bfa45c29709a7f4b7dc1e91aa25ca57
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440741"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Atribuir um usuário às funções de administrador no Azure Active Directory
Este artigo explica como atribuir uma função administrativa a um usuário no Azure AD (Azure Active Directory). Para obter informações sobre como adicionar novos usuários em sua organização, consulte [Adicionar novos usuários ao Azure Active Directory](../add-users-azure-active-directory.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário
1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global ou um administrador de funções com privilégios do diretório.

2. Selecione **Azure Active Directory**, selecione **Usuários** e então selecione um usuário específico na lista.

    ![Abrir o gerenciamento de usuários](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Para o usuário selecionado, selecione **Função do diretório**, selecione **Adicionar função** e então selecione as funções de administrador apropriadas na lista **Funções de diretório**, como  **Administrador de acesso condicional**. Para saber mais sobre as funções de usuário e administrador, veja [Como atribuir funções de administrador no Azure AD](../users-groups-roles/directory-assign-admin-roles.md). 

    ![Atribuindo um usuário a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Pressione **Selecionar** para salvar.

## <a name="next-steps"></a>Próximas etapas
* [Início rápido: Adicionar ou excluir usuários no Azure Active Directory](add-users-azure-active-directory.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) 
* [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurar um usuário excluído](active-directory-users-restore.md)
