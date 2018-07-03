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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0aec1f50225bea25d5f68289c22ccc7b69703f7a
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317939"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gerenciar acesso usando o RBAC e o Portal do Azure

O [Controle de acesso baseado em função (RBAC)](overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Este artigo descreve como gerenciar o acesso de usuários, grupos e aplicativos usando o RBAC e o Portal do Azure.

## <a name="list-roles"></a>Listar funções

Uma definição de função é uma coleção de permissões que podem ser usadas para atribuições de função. O Azure tem mais de 60 [funções internas](built-in-roles.md).

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

   ![Opção Funções](./media/role-assignments-portal/list-subscription-access-control.png)

1. Escolha **Funções** para ver uma lista de todas as funções internas e personalizadas.

   ![Opção Funções](./media/role-assignments-portal/roles-option.png)

   Você pode ver o número de usuários e grupos que estão atribuídos a cada função.

   ![Lista de funções](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Relacionar acesso

Ao gerenciar o acesso, você deseja saber quem tem acesso, quais são suas permissões e em que nível. Para listar o acesso, liste as atribuições de função.

### <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de função de um usuário

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

    Na folha Controle de acesso (IAM), também conhecida como gerenciamento de identidade e acesso, você pode ver quem tem acesso a essa assinatura e à função.

    ![Folha Controle de acesso (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Os administradores e coadministradores de assinatura clássicos são, na realidade, os proprietários da assinatura no novo modelo de RBAC.


### <a name="list-role-assignments-for-a-resource-group"></a>Listar as atribuições para um grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha um grupo de recursos e escolha **Controle de acesso (IAM)**.

   Na folha Controle de acesso (IAM), você pode ver quem tem acesso a esse grupo de recursos. Observe que algumas funções são definidas para **Este recurso** enquanto outras são **(Herdadas)** de outro escopo. O acesso é atribuído especificamente ao grupo de recursos ou herdado de uma atribuição à assinatura pai.

   ![Grupos de recursos](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

1. Na lista de navegação, escolha **Azure Active Directory**.

1. Escolha **Usuários** para abrir **Todos os usuários**.

   ![Folha Todos os usuários do Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Escolha um usuário específico na lista.

1. Na seção **Gerenciar**, escolha **Recursos do Azure**.

   ![Recursos do Azure para usuário do Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   Na folha de recursos do Azure, você pode ver as atribuições de função para o usuário selecionado. Essa lista inclui apenas as atribuições de função para os recursos que você tem permissão para ler. Por exemplo, se o usuário também tem atribuições de função em uma assinatura diferente você não pode ler, as atribuições de função não aparecerão na lista.

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você cria uma atribuição de função.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Criar uma atribuição de função em um escopo de assinatura

1. No Portal do Azure, escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha Controle de acesso (IAM) para o grupo de recursos](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para criar a atribuição de função.

   Após alguns instantes, a entidade de segurança é atribuída à função no escopo da assinatura.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Criar uma atribuição de função em um escopo de grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha um grupo de recursos.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função no escopo do grupo de recursos.

   ![Folha Controle de acesso (IAM) para o grupo de recursos](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/role-assignments-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo ou aplicativo. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Escolha **Salvar** para criar a atribuição de função.

   Após alguns instantes, a entidade de segurança é atribuída à função no escopo do grupo de recursos.

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você deve remover uma atribuição de função.

### <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

1. Abra a folha **Controle de acesso (IAM)** da assinatura, o grupo de recursos ou o recurso que tem a atribuição de função que você deseja remover.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Escolha **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de remoção de atribuição de função que aparece, escolha **Sim**.

As atribuições herdadas não podem ser removidas. Se você precisar remover uma atribuição de função herdada, precisará fazê-lo no escopo em que a atribuição de função foi criada. Na coluna **Escopo**, ao lado de **Herdado**, há um link que leva aos recursos em que essa função foi atribuída. Vá para o escopo listado a fim de remover a atribuição de função.

## <a name="other-tools-to-manage-access"></a>Outras ferramentas para gerenciar o acesso

Você pode atribuir funções e gerenciar o acesso com comandos do RBAC do Azure em ferramentas que não sejam o portal do Azure. Para obter mais informações, consulte os links a seguir:

* [PowerShell do Azure](role-assignments-powershell.md)
* [CLI do Azure](role-assignments-cli.md)
* [API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Conceder acesso a um usuário usando o RBAC e o Portal do Azure](quickstart-assign-role-user-portal.md)
* [Tutorial: conceder acesso a um usuário usando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Funções internas](built-in-roles.md)
