---
title: Atribuir funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610aeec9e4c40d0aad0c28f02697e2cf01edbe4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493973"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Atribuir funções de recurso do Azure no PIM

Azure Active Directory (Azure AD) PIM Privileged Identity Management () pode gerenciar as funções de recurso do Azure internas, bem como funções personalizadas, incluindo (mas não limitado a):

- Proprietário
- Administrador de Acesso do Usuário
- Colaborador
- Administrador de Segurança
- Gerenciador de Segurança e muito mais

> [!NOTE]
> Os usuários ou membros de um grupo atribuído às funções de Administrador de Acesso de Usuário ou Proprietário e Administradores Globais que permitem o gerenciamento de assinatura no Azure AD são Administradores de Recursos. Esses administradores podem atribuir funções, definir configurações de função e revisar o acesso ao usar o PIM para recursos do Azure. Exiba a lista de [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obter informações sobre como permitir acesso para que outro administrador gerencie o PIM, veja [conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

    Se você ainda não iniciou o PIM no portal do Azure, acesse [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

    ![Lista de recursos do Azure para gerenciar](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Clique no recurso que você quer gerenciar, como uma assinatura ou um grupo de gerenciamento.

1. Em Gerenciar, clique em **Funções** para ver a lista de funções dos recursos do Azure.

    ![Funções dos recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Clique em **Adicionar membro** para abrir o novo painel de atribuição.

1. Clique em **Selecionar uma função** para abrir o painel Selecionar uma função.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Clique em uma função que você quer atribuir e, em seguida, clique em **Selecionar**.

    O painel Selecionar um membro ou grupo é aberto.

1. Clique em um membro ou grupo que você quer atribuir à função e, em seguida, clique em **Selecionar**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel de configurações de Associação é aberto.

1. Na lista **Tipo de atribuição**, selecione **Qualificado** ou **Ativo**.

    ![Painel de configurações das Associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    O PIM para recursos do Azure fornece dois tipos distintos de atribuição:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - Atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Se uma atribuição permanente for necessária (permanentemente qualificada ou permanentemente atribuída), selecione a caixa de seleção **Permanentemente**.

    Dependendo das configurações de função, a caixa de seleção poderá não aparecer ou não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

    ![Configurações de associação - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Ao terminar, clique em **Concluído**.

    ![Nova atribuição - Adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, clique em **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso que você quer gerenciar, como uma assinatura ou um grupo de gerenciamento.

1. Em Gerenciar, clique em **Funções** para ver a lista de funções dos recursos do Azure.

    ![Funções de recursos do Azure - Selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Clique na função que você quer atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Clique em **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Próximos passos

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
