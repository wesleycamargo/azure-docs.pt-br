---
title: 'Início Rápido: Conceder acesso a um usuário usando o RBAC e o portal do Azure | Microsoft Docs'
description: Use o RBAC (controle de acesso baseado em função) para conceder permissões a um usuário atribuindo uma função no portal do Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 74ecca671409b6e163bc0db29d66167d240b645c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092515"
---
# <a name="quickstart-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Início Rápido: Conceder acesso a um usuário usando o RBAC e o portal do Azure

O RBAC (controle de acesso baseado em função) é a maneira de gerenciar o acesso aos recursos no Azure. Neste início rápido, você concede acesso a um usuário para criar e gerenciar máquinas virtuais em um grupo de recursos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em http://portal.azure.com.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha **Adicionar** para abrir a folha **Grupo de recursos**.

   ![Adicionar um novo grupo de recursos](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Para **Nome do grupo de recursos**, insira **rbac-quickstart-resource-group**.

1. Selecione uma assinatura e localização.

1. Escolha **Criar** para criar o grupo de recursos.

1. Escolha **Atualizar** para atualizar a lista de grupos de recursos.

   O novo grupo de recursos é exibido em sua lista de grupos de recursos.

   ![Lista de grupos de recursos](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você cria uma atribuição de função.

1. Na lista de **Grupos de recursos**, escolha o novo grupo de recursos **rbac-quickstart-resource-group**.

1. Escolha **Controle de acesso (IAM)** para ver a lista atual de atribuições de função.

   ![Folha Controle de acesso (IAM) para o grupo de recursos](./media/quickstart-assign-role-user-portal/access-control.png)

1. Escolha **Adicionar** para abrir o painel **Adicionar permissões**.

   Se você não tiver permissões para atribuir funções, não verá a opção **Adicionar**.

   ![Painel Adicionar permissões](./media/quickstart-assign-role-user-portal/add-permissions.png)

1. Na lista suspensa **Função**, selecione **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione você mesmo ou outro usuário.

1. Escolha **Salvar** para criar a atribuição de função.

   Após alguns instantes, a função Colaborador da Máquina Virtual é atribuída ao usuário no escopo do grupo de recursos rbac-quickstart-resource-group.

   ![Atribuição de função Colaborador da Máquina Virtual](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você deve remover uma atribuição de função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário com a função de Colaborador da Máquina Virtual.

1. Escolha **Remover**.

   ![Remover mensagem de atribuição de função](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Na mensagem de remoção de atribuição de função que aparece, escolha **Sim**.

## <a name="clean-up"></a>Limpar

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha **rbac-quickstart-resource-group** para abrir o grupo de recursos.

1. Escolha **Excluir grupo de recursos** para excluir o grupo de recursos.

   ![Excluir grupo de recursos](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na folha **Tem certeza de que deseja excluir**, digite o nome do grupo de recursos: **rbac-quickstart-resource-group**.

1. Escolha **Excluir** para excluir o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a um usuário usando o RBAC e o PowerShell](tutorial-role-assignments-user-powershell.md)

