<properties
	pageTitle="Gerenciar função com base no Controle de Acesso (RBAC) com o Azure PowerShell | Microsoft Azure"
	description="Como gerenciar o RBAC com o Azure PowerShell, incluindo listagem e atribuição e funções, e exclusão de atribuições de funções."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Gerenciar o Controle de Acesso baseado em função com o Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)


O RBAC (Controle de Acesso Baseado em Função) no Portal do Azure e na API de Gerenciamento de Recursos do Azure permite que você gerencie o acesso à sua assinatura em um nível refinado. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Para poder usar o PowerShell para gerenciar o RBAC, você deve ter o seguinte:

- PowerShell do Azure, versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

- Cmdlets do Azure Resource Manager. Instale os [cmdlets do Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) no PowerShell.

## Listar funções

### Relacionar todas as funções disponíveis
Para relacionar as funções RBAC disponíveis para atribuição e inspecionar as operações para as quais elas concedem acesso, use:

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Relacionar ações de uma função
Para relacionar as ações para uma função específica, use:

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - Get-AzureRmRoleDefinition para uma função específica - captura de tela](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Veja quem tem acesso
Para listar as atribuições de acesso de RBAC, use:

    Get-AzureRmRoleAssignment

###	Listar as atribuições de função em um escopo específico
Você pode ver todas as atribuições de acesso para uma assinatura, grupo de recursos ou recurso especificados. Por exemplo, para ver todas as atribuições de ativas para um grupo de recursos, use:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - Get-AzureRmRoleAssignment para um grupo de recursos - captura de tela](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Listar funções atribuídas a um usuário
Para listar todas as funções atribuídas a um usuário especificado, incluindo as funções atribuídas aos grupos que ele pertence, use:

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![RBAC PowerShell - Get-AzureRmRoleAssignment para um usuário - captura de tela](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Relacionar atribuições de função de administrador e coadministrador de serviços clássicos
Para relacionar as atribuições para administrador e coadministradores da assinatura clássica, use:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Conceder acesso
### Pesquisar IDs de objeto
Para atribuir uma função, você precisa identificar o objeto (usuário, grupo ou aplicativo) e o escopo.

Se você não souber a ID da assinatura, você poderá encontrá-la na folha **Assinaturas** no Portal do Azure. Ou então, saiba como consultá-la com [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) no MSDN.

Para obter a ID de objeto para um grupo do AD do Azure, use:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obter a ID de objeto de uma Entidade de Serviço do Azure AD ou um aplicativo, use:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Atribuir função ao aplicativo no escopo da assinatura
Para conceder acesso a um aplicativo no escopo da assinatura, use:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Atribuir função ao usuário no escopo do grupo de recursos
Para conceder acesso a um usuário no escopo do grupo de recursos:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Atribuir função ao grupo no escopo de recursos
Para conceder acesso a um grupo no escopo de recursos, use:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Remover acesso
Para remover o acesso a usuários, grupos e aplicativos, use:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Criar função personalizada
Para criar uma função personalizada, use o comando `New-AzureRmRoleDefinition`.

O exemplo a seguir cria uma função personalizada chamada *Operador de Máquina Virtual* que concede acesso a todas as operações de leitura dos provedores de recurso *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede acesso para iniciar, reiniciar e monitorar máquinas virtuais. A função personalizada pode ser usada em duas assinaturas.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modificar uma função personalizada
Para modificar uma função personalizada, use o comando `Get-AzureRmRoleDefinition` para recuperar a definição da função. Em seguida, faça as alterações desejadas à definição de função. Por fim, use o comando `Set-AzureRmRoleDefinition` para salvar a definição da função modificada.

O exemplo a seguir adiciona a operação `Microsoft.Insights/diagnosticSettings/*` à função personalizada *Operador de Máquina Virtual*.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

O exemplo a seguir adiciona uma assinatura do Azure para os escopos atribuíveis da função personalizada de operador da Máquina Virtual.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Excluir uma função personalizada

Para excluir uma função personalizada, use o comando `Remove-AzureRmRoleDefinition`.

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual*.

![RBAC PowerShell - Remove-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Listar funções personalizadas
Para listar as funções disponíveis para atribuição em um escopo, use o comando `Get-AzureRmRoleDefinition`.

O exemplo a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

No exemplo a seguir, a função personalizada *Operador de Máquina Virtual* não está disponível na assinatura *Production4*, pois essa assinatura não está no **AssignableScopes** da função.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Consulte também
- [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md)[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->