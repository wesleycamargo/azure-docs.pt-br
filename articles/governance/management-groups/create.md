---
title: Criar grupos de gerenciamento para organizar recursos do Azure - Azure Governance
description: Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos usando o portal, o Azure PowerShell e a CLI do Azure.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 2dd2a6e071533deef47a6482bfb9ed92953864ba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259789"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para o gerenciamento e a organização de recursos

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Crie um grupo de gerenciamento

Você pode criar o grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure. Atualmente, é possível usar modelos do Resource Manager para criar grupos de gerenciamento.

### <a name="create-in-portal"></a>Criar no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Na página principal, selecione **Novo grupo de gerenciamento**.

   ![Página para trabalhar com grupos de gerenciamento](./media/main.png)

1. Preencha o campo de ID do grupo de gerenciamento.

   - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. O [grupo de gerenciamento raiz](index.md#root-management-group-for-each-directory) é criado automaticamente com uma ID que é a ID do Active Directory do Azure. Para todos os outros grupos de gerenciamento, atribuir uma ID exclusiva.
   - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

   ![Painel de opções para criar um novo grupo de gerenciamento](./media/create_context_menu.png)  

1. Clique em **Salvar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

Para o PowerShell, use o [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gerenciamento.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterado posteriormente.

Se você quiser que o grupo de gerenciamento para mostrar um nome diferente no portal do Azure, adicione a **DisplayName** parâmetro. Por exemplo, para criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição de "Contoso Group", use o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado sob o grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como o pai, use o **ParentId** parâmetro.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

CLI do Azure, use o [criar grupo de gerenciamento de conta de az](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) comando para criar um novo grupo de gerenciamento.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterado posteriormente.

Se você quiser que o grupo de gerenciamento para mostrar um nome diferente no portal do Azure, adicione a **nome de exibição** parâmetro. Por exemplo, para criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição de "Contoso Group", use o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado sob o grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como o pai, use o **pai** parâmetro e forneça o nome do grupo pai.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](create.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](manage.md)
- [Grupos de gerenciamento de análise no módulo de recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupos de gerenciamento de análise na API REST](/rest/api/resources/managementgroups)
- [Grupos de gerenciamento de análise na CLI do Azure](/cli/azure/account/management-group)