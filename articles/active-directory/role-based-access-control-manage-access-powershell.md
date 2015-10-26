<properties
	pageTitle="Gerenciar função com base no Controle de Acesso (RBAC) com o Azure PowerShell | Microsoft Azure"
	description="Como gerenciar RBAC com o Azure PowerShell, incluindo as atribuições de relacionar, atribuir e excluir funções."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Gerenciar função com base no Controle de Acesso (RBAC) com o Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Relacionar funções RBAC
### Relacionar todas as funções disponíveis
Para relacionar as funções RBAC disponíveis para atribuição e inspecionar as operações para as quais elas concedem acesso, use:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Relacionar ações de uma função
Para relacionar as ações para uma função específica, use:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Relacionar acesso
### Relacionar todas as atribuições de função na assinatura selecionada
Para relacionar as atribuições de acesso de RBAC como válidas na assinatura, recurso ou grupo de recursos, use:

    Get-AzureRoleAssignment

###	Relacionar as atribuições de função como efetivas em um grupo de recursos
Para relacionar as atribuições de acesso para um grupo de recursos, use:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Relacionar atribuições de função a um usuário, incluindo os que são atribuídos a grupos de usuários
Para relacionar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro, use:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Relacionar atribuições de função de administrador e coadministrador de serviços clássicos
Para relacionar as atribuições para administrador e coadministradores da assinatura clássica, use:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Conceder Acesso
### Pesquisar ids de objeto
Para usar as sequências de comando a seguir, você deve, primeiramente, encontrar as ids de objeto. Supõe-se que você já saiba a ID de assinatura com a qual você está trabalhando. Caso contrário, veja [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) no MSDN.

#### Localizar a ID de objeto para um grupo do AD do Azure
Para obter a ID de objeto para um grupo do AD do Azure, use:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Localizar a ID de objeto para uma entidade de serviço do AD do Azure
Para obter a ID de objeto para uma entidade de serviço do AD do Azure, use: Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Atribuir função ao grupo no escopo da assinatura
Para conceder acesso a um grupo no escopo da assinatura, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Atribuir função ao aplicativo no escopo da assinatura
Para conceder acesso a um aplicativo no escopo da assinatura, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Atribuir função ao usuário no escopo do grupo de recursos
Para conceder acesso a um usuário no escopo do grupo de recursos:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Atribuir função ao grupo no escopo de recursos
Para conceder acesso a um grupo no escopo de recursos, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Remover acesso
Para remover o acesso a usuários, grupos e aplicativos, use:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Tópicos RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->