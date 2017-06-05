---

title: "Atribuir um usuário às funções de administrador no Azure Active Directory | Microsoft Docs"
description: "Explica como alterar informações administrativas de usuário no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: ebbadff39c736ec7364999f7644694e8a177073f
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017


---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Atribuir um usuário às funções de administrador no Azure Active Directory
Este artigo explica como atribuir uma função administrativa a um usuário no Azure AD (Azure Active Directory). Para obter informações sobre como adicionar novos usuários em sua organização, consulte [Adicionar novos usuários ao Azure Active Directory](active-directory-users-create-azure-portal.md). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Na folha **Usuários e grupos**, selecione **Todos os usuários**.

   ![Abrindo a folha Todos os usuários](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Na folha **Usuários e grupos - Todos os usuários** , escolha um usuário na lista.
5. Na folha do usuário selecionado, selecione **Função de diretório**, em seguida, atribua o usuário a uma função na lista **Função de diretório**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

      ![Atribuindo um usuário a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* [Adicionar um usuário](active-directory-users-create-azure-portal.md)
* [Redefinir a senha do usuário no novo Portal do Azure](active-directory-users-reset-password-azure-portal.md)
* [Alterar as informações de trabalho do usuário](active-directory-users-work-info-azure-portal.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Excluir um usuário no Azure AD](active-directory-users-delete-user-azure-portal.md)

