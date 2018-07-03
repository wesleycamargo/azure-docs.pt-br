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
ms.openlocfilehash: 7ed564d5954841f96109568b33183908d25bb8be
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939499"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Atribuir um usuário às funções de administrador no Azure Active Directory
Este artigo explica como atribuir uma função administrativa a um usuário no Azure AD (Azure Active Directory). Para obter informações sobre como adicionar novos usuários em sua organização, consulte [Adicionar novos usuários ao Azure Active Directory](../add-users-azure-active-directory.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário
1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global ou um administrador de funções com privilégios do diretório.

2. Selecione **Azure Active Directory**, selecione **Usuários** e então selecione um usuário específico na lista.

    ![Abrir o gerenciamento de usuários](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Para o usuário selecionado, selecione **Função do diretório**, selecione **Adicionar função** e então selecione as funções de administrador apropriadas na lista **Funções de diretório**, como  **Administrador de acesso condicional**. Para saber mais sobre as funções de usuário e administrador, veja [Como atribuir funções de administrador no Azure AD](../active-directory-assign-admin-roles-azure-portal.md). 

    ![Atribuindo um usuário a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Pressione **Selecionar** para salvar.

## <a name="next-steps"></a>Próximas etapas
* [Início rápido: Adicionar ou excluir usuários no Azure Active Directory](add-users-azure-active-directory.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) 
* [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurar um usuário excluído](active-directory-users-restore.md)
