---
title: Criar grupos de gerenciamento para organizar recursos do Azure | Microsoft Docs
description: "Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: ae91ad29b867ad4ab00831ee40102bcec2fc890c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para o gerenciamento e a organização de recursos
Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../azure-policy/azure-policy-introduction.md) e os [Controles de Acesso Baseados em Função do Azure](../active-directory/role-based-access-control-what-is.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](management-groups-overview.md). 

O recurso do grupo de gerenciamento está disponível em uma visualização pública. Para começar a usar os grupos de gerenciamento, faça logon no [Portal do Azure](https://portal.azure.com) ou você pode usar [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) ou [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) para criar grupos de gerenciamento.   

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído.  

## <a name="how-to-create-a-management-group"></a>Como criar um grupo de gerenciamento
Você pode criar o grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure.

### <a name="create-in-portal"></a>Criar no portal

1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. Selecione **Todos os serviços** > **Grupos de gerenciamento**.
3. Na página principal, selecione **Novo grupo de gerenciamento.** 

    ![Criar Grupo](media/management-groups/create_main.png) 
4.  Preencha o campo de ID do grupo de gerenciamento. 
    - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. 
    - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

    ![Criar](media/management-groups/create_context_menu.png)  
5.  Selecione **Salvar**


### <a name="create-in-powershell"></a>Criar no PowerShell
No PowerShell, você usa os cmdlets Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não poderá ser alterada posteriormente.

Se quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, você deverá adicionar o parâmetro **DisplayName** com a cadeia de caracteres. Por exemplo, se quisesse criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição de "Contoso Group", você usaria o seguinte cmdlet: 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Use o parâmetro **ParentId** para criar esse grupo de gerenciamento sob um gerenciamento diferente.  

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure
Na CLI do Azure, você usa o comando az account management-group create. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre grupos de gerenciamento, consulte: 
- [Organizar seus recursos com grupos de gerenciamento do Azure ](management-groups-overview.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](management-groups-manage.md)
- [Instalar o módulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Revisar as Especificações de API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Instalar a Extensão CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
