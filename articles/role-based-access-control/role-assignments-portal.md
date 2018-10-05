---
title: Gerenciar acesso usando o RBAC e o Portal do Azure | Microsoft Docs
description: Saiba como gerenciar acesso de usuários, grupos e aplicativos usando o RBAC (controle de acesso baseado em função) e o portal do Azure. Isso inclui como listar o acesso, conceder o acesso e remover o acesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cac4e4cee408e5208d2d5d84f81b8ad7a89f03b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033984"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gerenciar acesso usando o RBAC e o Portal do Azure

O [Controle de acesso baseado em função (RBAC)](overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Este artigo descreve como gerenciar o acesso de usuários, grupos e aplicativos usando o RBAC e o Portal do Azure.

## <a name="list-roles"></a>Listar funções

Uma definição de função é uma coleção de permissões que podem ser usadas para atribuições de função. O Azure tem mais de 70 [funções internas](built-in-roles.md). Siga estas etapas para listar as funções no portal.

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

   ![Opção Funções](./media/role-assignments-portal/list-subscription-access-control.png)

1. Escolha **Funções** para ver uma lista de todas as funções internas e personalizadas.

   ![Opção Funções](./media/role-assignments-portal/roles-option.png)

   Você pode ver o número de usuários e grupos que estão atribuídos a cada função.

   ![Lista de funções](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Relacionar acesso

Ao gerenciar o acesso, você deseja saber quem tem acesso, quais são suas permissões e em que nível. Para listar o acesso, liste as atribuições de função. Siga estas etapas para listar o acesso de usuários e listar o acesso em escopos diferentes.

### <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

1. Na lista de navegação, escolha **Azure Active Directory**.

1. Escolha **Usuários** para abrir **Todos os usuários**.

   ![Folha Todos os usuários do Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Escolha um usuário específico na lista.

1. Na seção **Gerenciar**, escolha **Recursos do Azure**.

   ![Recursos do Azure para usuário do Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   Na folha de recursos do Azure, você pode ver as atribuições de função para o usuário selecionado e a assinatura selecionada. Essa lista inclui apenas as atribuições de função para os recursos que você tem permissão para ler. Por exemplo, se o usuário também tiver atribuições de função que você não pode ler, as atribuições de função não aparecerão na lista.

1. Se você tiver várias assinaturas, poderá escolher a lista suspensa **Assinatura** para ver as atribuições de função em uma assinatura diferente.

### <a name="list-role-assignments-for-a-resource-group"></a>Listar as atribuições para um grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha um grupo de recursos e escolha **Controle de acesso (IAM)**.

   No blade Controle de acesso (IAM), também conhecido como gerenciamento de identidade e acesso, é possível ver quem tem acesso a esse grupo de recursos. Observe que algumas funções são definidas para **Este recurso** enquanto outras são **(Herdadas)** de outro escopo. O acesso é atribuído especificamente ao grupo de recursos ou herdado de uma atribuição à assinatura pai.

   ![Grupos de recursos](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de função de um usuário

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

    No blade Controle de acesso (IAM), você pode ver quem tem acesso a essa assinatura e sua função.

    ![Folha IAM (controle) de acesso para uma assinatura](./media/role-assignments-portal/subscription-access-control.png)

    Os administradores de assinatura clássicos e os coadministradores são considerados proprietários da assinatura no modelo RBAC.

### <a name="list-role-assignments-for-a-management-group"></a>Listar as atribuições de função para um grupo de gerenciamento

1. No portal do Azure, escolha **todos os serviços** e, em seguida **grupos de gerenciamento**.

1. Escolha seu grupo de gerenciamento.

1. Escolher **(detalhes)** para seu grupo de gerenciamento selecionado.

    ![Grupos de gerenciamento](./media/role-assignments-portal/management-groups-list.png)

1. Clique em **Controle de acesso (IAM)**.

    No blade Controle de acesso (IAM), você pode ver quem tem acesso a esse grupo de gerenciamento e sua função.

    ![Lâmina de controle de acesso (IAM) para um grupo de gerenciamento](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você atribui uma função. Siga estas etapas para conceder acesso em diferentes escopos.

### <a name="assign-a-role-at-a-resource-group-scope"></a>Atribuir uma função em um escopo de grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha um grupo de recursos.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo do grupo de recursos.

   ![Lâmina de controle de acesso (IAM) para um grupo de recursos](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída à função no escopo do grupo de recursos.

### <a name="assign-a-role-at-a-subscription-scope"></a>Atribuir uma função em um escopo de assinatura

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída à função no escopo da assinatura.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um usuário como um administrador de uma assinatura

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função Proprietário permite ao usuário acesso completo a todos os recursos na assinatura, inclusive o direito de delegar acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

1. Escolha **Salvar** para atribuir a função.

   Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

### <a name="assign-a-role-at-a-management-group-scope"></a>Atribuir uma função em um escopo de grupo de gerenciamento

1. No portal do Azure, escolha **todos os serviços** e, em seguida **grupos de gerenciamento**.

1. Escolha seu grupo de gerenciamento.

1. Escolher **(detalhes)** para seu grupo de gerenciamento selecionado.

    ![Grupos de gerenciamento](./media/role-assignments-portal/management-groups-list.png)

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Lâmina de controle de acesso (IAM) para um grupo de gerenciamento](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions-management-groups.png)

1. Na lista suspensa **Função**, selecione uma função como **Colaborador do grupo de gerenciamento**.

    Para obter informações sobre as ações com suporte em grupos de gerenciamento para várias funções, consulte [organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/index.md#management-group-access).

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída à função no escopo do grupo de gerenciamento.

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você deve remover uma atribuição de função. Siga estas etapas para remover o acesso.

### <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

1. Abra o blade **Controle de acesso (IAM)** para o grupo de gerenciamento, assinatura, grupo de recursos ou recurso que possui a atribuição de função que você deseja remover.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Escolha **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de remoção de atribuição de função que aparece, escolha **Sim**.

    As atribuições herdadas não podem ser removidas. Se você precisar remover uma atribuição de função herdada, precisará fazê-lo no escopo em que a atribuição de função foi criada. No **escopo** coluna, próximo a **(herdado)** há um link que leva você para o escopo em que essa função foi atribuída. Vá para o escopo listado a fim de remover a atribuição de função.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Conceder acesso a um usuário usando o RBAC e o Portal do Azure](quickstart-assign-role-user-portal.md)
* [Tutorial: conceder acesso a um usuário usando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Funções internas](built-in-roles.md)
* [Organizar seus recursos com grupos de gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md)
