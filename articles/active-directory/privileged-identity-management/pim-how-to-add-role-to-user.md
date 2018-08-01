---
title: Atribuir funções do diretório a usuários usando o Azure AD PIM | Microsoft Docs
description: Saiba como atribuir funções do diretório a usuários usando o Azure Active Directory Privileged Identity Management e o portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1834addb4e51030afda43a2d7acad5d7ffc1889a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226689"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Atribuir funções do diretório a usuários usando o Azure AD PIM

Com o Azure AD (Azure Active Directory), um Administrador Global pode tornar atribuições de funções do diretório **permanentes**. Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço Azure AD Privileged Identity Management (PIM) permite que os administradores de função com privilégios também tornem atribuições de funções do diretório permanentes. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para funções do diretório. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas. Para obter informações sobre as funções possíveis de gerenciar usando o PIM, consulte [Funções de diretório que você pode gerenciar usando o Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para qualificar um usuário para uma função do diretório do AD do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obter informações sobre como conceder outro acesso de usuário para gerenciar o PIM, consulte [Como conceder acesso ao PIM](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

    Se você ainda não habilitou o PIM no portal do Azure, acesse [Introdução ao Azure AD PIM](pim-getting-started.md).

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Função (visualização)** ou **Membros**.

    ![Funções do diretório do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Clique em **Adicionar Membro** para abrir Adicionar membros gerenciados.

1. Clique em **Selecionar uma função**, clique em uma função que você quer gerenciar e, em seguida, clique em **Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Clique em **Selecionar membros**, selecione os usuários que você quer atribuir à função e, em seguida, clique em **Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em Adicionar membros gerenciados, clique em **OK** para adicionar o usuário à função.

     Quando a função for atribuída, o usuário que você selecionou aparecerá na lista de membros como **qualificado** para a função.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para uma função, avise-o que é possível ativá-la de acordo com as instruções em [Como ativar ou desativar uma função](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a registrar na MFA (Autenticação Multifator do Microsoft Azure) durante a ativação. Se um usuário não puder se registrar na MFA ou estiver usando uma conta da Microsoft (geralmente, @outlook.com), será preciso torná-lo permanente em todas as funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, os novos usuários somente são qualificados para uma função do diretório. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique em uma função **qualificada** que você deseja tornar permanente.

1. Clique em **Mais** e então clique em **Tornar perm**.

    ![Tornar a atribuição de função permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

É possível remover usuários de atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um Administrador Global permanente. Se não tiver certeza de quais os usuários ainda precisam das suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função do diretório.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique em uma atribuição de função que você queira remover.

1. Clique em **Mais** e, em seguida, clique em **Remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que pede confirmação, clique em **Sim**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função será removida.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
