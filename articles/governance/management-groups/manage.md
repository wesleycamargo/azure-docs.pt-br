---
title: Como alterar, excluir ou gerenciar seus grupos de gerenciamento no Azure – Azure Governance
description: Saiba como visualizar, manter, atualizar e excluir sua hierarquia de grupos de gerenciamento.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: dbfb6ecb9f29a82a8871922982a64dbefc338969
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342568"
---
# <a name="manage-your-resources-with-management-groups"></a>Gerenciar seus recursos com grupos de gerenciamento

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Você pode alterar, excluir e gerenciar esses contêineres para ter hierarquias que podem ser usadas com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função (RBAC) do Azure](../../role-based-access-control/overview.md). Para saber mais sobre grupos de gerenciamento, consulte [organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

Para fazer alterações em um grupo de gerenciamento, você deve ter uma função de Proprietário ou Colaborador no grupo de gerenciamento. Para ver quais permissões você tem, selecione o grupo de gerenciamento e, em seguida, selecione **IAM**. Para saber mais sobre as funções de RBAC, consulte [Gerenciar acesso e permissões com RBAC](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Alterar o nome de um grupo de gerenciamento

Você pode alterar o nome do grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure.

### <a name="change-the-name-in-the-portal"></a>Alterar o nome no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que gostaria de renomear.

1. Selecione a opção **Renomear grupo** na parte superior da página.

   ![Renomear opção de grupo](./media/detail_action_small.png)

1. Quando o menu for aberto, digite o novo nome que gostaria de exibir.

   ![Renomear painel de grupo](./media/rename_context.png)

1. Clique em **Salvar**.

### <a name="change-the-name-in-powershell"></a>Alterar o nome no PowerShell

Para atualizar o nome de exibição, use **Update-AzureRmManagementGroup**. Por exemplo, para alterar um nome de grupos de gerenciamento de "Contoso IT" para "Contoso Group", execute o seguinte comando:

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Alterar o nome na CLI do Azure

Para a CLI do Azure, use o comando de atualização.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Excluir um grupo de gerenciamento

Para a exclusão de um grupo de gerenciamento, os seguintes requisitos deverão ser atendidos:

1. Não existem grupos de gerenciamento filhos ou assinaturas no grupo de gerenciamento.

   - Para mover uma assinatura de um grupo de gerenciamento, confira [Mover assinatura para outro grupo de gerenciamento](#Move-subscriptions-in-the-hierarchy).

   - Para mover um grupo de gerenciamento para outro grupo de gerenciamento, consulte [Mover grupos de gerenciamento na hierarquia](#Move-management-groups-in-the-hierarchy).

1. Você tem permissões de gravação na função de Proprietário ou Colaborador do grupo de gerenciamento no grupo de gerenciamento. Para ver quais permissões você tem, selecione o grupo de gerenciamento e, em seguida, selecione **IAM**. Para saber mais sobre as funções de RBAC, consulte [Gerenciar acesso e permissões com RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Excluir no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que gostaria de excluir.

1. Selecione **Excluir**

   - Se o ícone estiver desativado, passar o seletor de mouse sobre o ícone mostrará o motivo.

   ![Excluir opção de grupo](./media/delete.png)

1. Há uma janela que abre confirmando que deseja excluir o grupo de gerenciamento.

   ![Janela de confirmação de exclusão de grupo](./media/delete_confirm.png)

1. Selecione **Sim**.

### <a name="delete-in-powershell"></a>Excluir no PowerShell

Use o comando **Remove-AzureRmManagementGroup** do PowerShell para excluir grupos de gerenciamento.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Exclusão na CLI do Azure

Com a CLI do Azure, use o comando az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Exibir grupos de gerenciamento

Você pode exibir qualquer grupo de gerenciamento no qual você tem uma função de RBAC direta ou herdada.  

### <a name="view-in-the-portal"></a>Exibir no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. A página de hierarquia do grupo de gerenciamento carrega de onde você pode explorar todas as assinaturas que você tem acesso a e grupos de gerenciamento. Selecionar o nome do grupo leva você a um nível abaixo da hierarquia. A navegação funciona da mesma forma que um explorador de arquivos.

   ![Principal](./media/main.png)

1. Para ver os detalhes do grupo de gerenciamento, selecione o link **(detalhes)** ao lado do título do grupo de gerenciamento. Se esse link não estiver disponível, você não tem permissões para exibir esse grupo de gerenciamento.  

### <a name="view-in-powershell"></a>Exibir no PowerShell

Você pode usar o comando Get-AzureRmManagementGroup para recuperar todos os grupos.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Para obter informações de um único grupo de gerenciamento, use o parâmetro -GroupName

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="view-in-azure-cli"></a>Exibir na CLI do Azure

Você pode usar o comando list para recuperar todos os grupos.  

```azurecli-interactive
az account management-group list
```

Para obter informações de um único grupo de gerenciamento, use o comando show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Mover assinaturas na hierarquia

Um motivo para criar um grupo de gerenciamento é agrupar assinaturas. Somente grupos de gerenciamento e assinaturas podem ser tornados filhos de outro grupo de gerenciamento. Uma assinatura movida para um grupo de gerenciamento herda todos os acessos de usuário e políticas do grupo de gerenciamento pai.

Para mover a assinatura, há várias permissões que você deve ter:

- Função de "Proprietário" na assinatura filho.
- Função de "Proprietário" ou "Colaborador" no novo grupo de gerenciamento pai.
- Função de "Proprietário" ou "Colaborador" no antigo grupo de gerenciamento pai.

Para ver quais permissões você tem, selecione o grupo de gerenciamento e, em seguida, selecione **IAM**. Para saber mais sobre as funções de RBAC, consulte [Gerenciar acesso e permissões com RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Mover assinaturas no portal

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Adicionar uma assinatura existente a um grupo de gerenciamento

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento o qual planeja que seja o pai.

1. Na parte superior da página, selecione **Adicionar assinatura**.

1. Selecione a assinatura na lista com a ID correta.

   ![Filhos](./media/add_context_sub.png)

1. Selecione "Salvar".

#### <a name="remove-a-subscription-from-a-management-group"></a>Remover uma assinatura de um grupo de gerenciamento

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você está planejando que é o pai atual.  

1. Selecione a elipse no final da linha da assinatura na lista que você deseja mover.

   ![Opção de mover](./media/move_small.png)

1. Selecione **Mover**.

1. No menu aberto, selecione o **Grupo de gerenciamento pai**.

   ![Mover painel](./media/move_small_context.png)

1. Clique em **Salvar**.

### <a name="move-subscriptions-in-powershell"></a>Mover assinaturas no PowerShell

Para mover uma assinatura no PowerShell, use o comando New-AzureRmManagementGroupSubscription command.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para remover o vínculo entre a assinatura e o grupo de gerenciamento, use o comando Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover assinaturas na CLI do Azure

Para mover uma assinatura na CLI, use o comando add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Para remover a assinatura do grupo de gerenciamento, use o comando subscription remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Mover grupos de gerenciamento na hierarquia  

Quando você move um grupo de gerenciamento pai, todos os recursos filhos que contêm grupos de gerenciamento, assinaturas, grupos de recursos e recursos são movidos com o pai.

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de gerenciamento no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento o qual planeja que seja o pai.

1. Na parte superior da página, selecione **Adicionar grupo de gerenciamento**.

1. No menu que será aberto, selecione se deseja usar um grupo de gerenciamento novo ou existente.

   - A seleção de um novo criará um novo grupo de gerenciamento.
   - A seleção de um existente apresentará uma lista suspensa de todos os grupos de gerenciamento que você pode mover para esse grupo de gerenciamento.  

   ![mover](./media/add_context_MG.png)

1. Clique em **Salvar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de gerenciamento no PowerShell

Use o comando Update-AzureRmManagementGroup no PowerShell para mover um grupo de gerenciamento em um grupo diferente.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Mover grupos de gerenciamento na CLI do Azure

Use o comando update para mover um grupo de gerenciamento com a CLI do Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditar grupos de gerenciamento usando logs de atividades

Para controlar os grupos de gerenciamento por meio desta API, use a [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs). No momento não é possível usar o PowerShell, a CLI ou o portal do Azure para controlar a atividade de grupos de gerenciamento.

1. Como um administrador de locatários do locatário do AD do Azure, [eleve o acesso](../../role-based-access-control/elevate-access-global-admin.md), em seguida, atribua uma função de leitor para o usuário de auditoria sobre o escopo `/providers/microsoft.insights/eventtypes/management`.
1. Como o usuário de auditoria, chame a [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs) para ver as atividades do grupo de gerenciamento. Convém filtrar pelo Provedor de Recursos **Microsoft.Management** para todas as atividades do grupo de gerenciamento.  Exemplo:

```xml
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Management'"
```

> [!NOTE]
> Para chamar convenientemente esta API da linha de comando, tente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](create.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](manage.md)
- [Analisar grupos de gerenciamento no Módulo de Recursos do Azure PowerShell](https://aka.ms/mgPSdocs)
- [Revisar grupos de gerenciamento na API REST](https://aka.ms/mgAPIdocs)
- [Revisar grupos de gerenciamento na CLI do Azure](https://aka.ms/mgclidoc)
