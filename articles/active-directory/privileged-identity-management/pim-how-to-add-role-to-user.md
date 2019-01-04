---
title: Atribuir funções de diretório do Azure AD no PIM | Microsoft Docs
description: Saiba como atribuir funções do diretório do Azure AD no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 2b099e1377536b46229b75f25d04ab2c1beb5c11
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724937"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>Atribuir funções de diretório do Azure AD no PIM

Com o Azure AD (Azure Active Directory), um Administrador Global pode tornar atribuições de funções do diretório **permanentes**. Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço Azure AD Privileged Identity Management (PIM) permite que os administradores de função com privilégios também tornem atribuições de funções do diretório permanentes. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para funções do diretório. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas. Para obter informações sobre as funções possíveis de gerenciar usando o PIM, veja [Funções de diretório do Azure AD que você pode gerenciar no PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para qualificar um usuário para uma função do diretório do AD do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obter informações sobre como permitir acesso para que outro administrador gerencie o PIM, veja [conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

    Se você ainda não iniciou o PIM no portal do Azure, acesse [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Funções** ou **Membros**.

    ![Funções do diretório do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Clique em **Adicionar Membro** para abrir Adicionar membros gerenciados.

1. Clique em **Selecionar uma função**, clique em uma função que você quer gerenciar e, em seguida, clique em **Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Clique em **Selecionar membros**, selecione os usuários que você quer atribuir à função e, em seguida, clique em **Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em Adicionar membros gerenciados, clique em **OK** para adicionar o usuário à função.

1. Na lista de funções, clique na função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário que você selecionou aparecerá na lista de membros como **Qualificado** para a função.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para uma função, avise-o que é possível ativá-la de acordo com as instruções em [Ativar minhas funções do diretório do Azure AD no PIM](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a registrar na MFA (Autenticação Multifator do Microsoft Azure) durante a ativação. Se um usuário não puder se registrar na MFA ou estiver usando uma conta da Microsoft (geralmente, @outlook.com), será preciso torná-lo permanente em todas as funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, os novos usuários somente são qualificados para uma função do diretório. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique em uma função **Qualificada** que você deseja tornar permanente.

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

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você tiver habilitado recentemente o PIM para uma assinatura e receber um erro de autorização ao tentar tornar um usuário qualificado para uma função do diretório, isso poderá ocorrer porque a entidade de serviço do MS-PIM ainda não tem as permissões apropriadas. A entidade de serviço do MS-PIM precisa ter a função de [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outros usuários. Em vez de esperar até que o MS-PIM receba a função de Administrador de Acesso do Usuário, atribua-a manualmente.

Siga estas etapas para atribuir a função de Administrador de Acesso do Usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como Administrador Global.

1. Escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

1. Escolha **Atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** tem a função **Administrador de Acesso do Usuário**.

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

1. Na lista suspensa **Função**, selecione a função **Administrador de Acesso do Usuário**.

1. Na lista **Selecionar**, localize e selecione a entidade de serviço **MS-PIM**.

   ![Adicionar permissões para o MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Salvar** para atribuir a função.

   Após alguns momentos, verifique se a entidade de serviço do MS-PIM tem a função Administrador de Acesso do Usuário no escopo da assinatura.

   ![Função Administrador de Acesso do Usuário para o MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função do diretório do Azure AD no PIM](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
