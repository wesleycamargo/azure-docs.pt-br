---
title: Criar grupos de gerenciamento para organizar recursos do Azure
description: Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2018
ms.author: rithorn
ms.openlocfilehash: c2d4317bcbf70a0cebf6ab1915968eeb9ef8b4c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992619"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para o gerenciamento e a organização de recursos

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído.

## <a name="create-a-management-group"></a>Crie um grupo de gerenciamento

Você pode criar o grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure. Atualmente, é possível usar modelos do Resource Manager para criar grupos de gerenciamento.

### <a name="create-in-portal"></a>Criar no portal

1. Faça logon no [Portal do Azure](http://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Na página principal, selecione **Novo grupo de gerenciamento**.

   ![Grupo Principal](./media/main.png)

1. Preencha o campo de ID do grupo de gerenciamento.

   - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo.
   - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

   ![Criar](./media/create_context_menu.png)  

1. Clique em **Salvar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

No PowerShell, você deve usar os cmdlets Add-AzureRmManagementGroups:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não poderá ser alterada posteriormente.

Se quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, você deverá adicionar o parâmetro **DisplayName** com a cadeia de caracteres. Por exemplo, se quisesse criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição de "Contoso Group", você usaria o seguinte cmdlet:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Use o parâmetro **ParentId** para criar esse grupo de gerenciamento sob um gerenciamento diferente.

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

Na CLI do Azure, você usa o comando az account management-group create.

```azurecli-interactive
az account management-group create --group-name 'Contoso'
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](manage.md)
- [Instalar o módulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Revisar as Especificações de API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Instalar a Extensão CLI do Azure](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)