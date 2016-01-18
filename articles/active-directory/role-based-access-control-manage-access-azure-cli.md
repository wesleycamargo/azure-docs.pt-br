<properties
	pageTitle="Gerenciar função com base no Controle de Acesso (RBAC) com o CLI do Azure | Microsoft Azure"
	description="Saiba como gerenciar o acesso baseado em função (RBAC) com a interface de linha de comando do Azure ao relacionar as funções e ações de função, atribuir funções às assinaturas e escopos de aplicativo."
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
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Gerenciar função com base no Controle de Acesso (RBAC) com a Interface de Linha de Comando (CLI) do Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Relacionar funções RBAC
###	Relacionar todas as funções disponíveis
Para relacionar todas as funções disponíveis, use:

		azure role list

O exemplo a seguir mostra a relação de *todas as funções disponíveis*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Relacionar ações de uma função
Para relacionar as ações de uma função, use:

    azure role show <role in quotes>

O exemplo a seguir mostra as ações do *Colaborador* e as funções do *Colaborador da máquina virtual*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Relacionar acesso
###	Relacionar as atribuições de função como efetivas em um grupo de recursos
Para relacionar as atribuições de função como efetivas em um grupo de recursos, use:

    azure role assignment list --resource-group <resource group name>

O exemplo a seguir mostra as atribuições de função efetivas no grupo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Relacionar atribuições de função a um usuário, incluindo os que são atribuídos a um grupo de usuários

O exemplo a seguir mostra as atribuições de função efetivas no grupo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Conceder acesso
Depois de ter identificado a função que você deseja atribuir para conceder acesso, use:

    azure role assignment create

###	Atribuir função ao grupo no escopo da assinatura
Para atribuir função ao grupo no escopo da assinatura, use:

   azure role assignment create --objId <group's object id> --role <name of role> --scope <subscription/subscription id>

O exemplo a seguir atribui a função *Leitor* para a *equipe de Christine Koch* no escopo da *assinatura*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Atribuir função ao aplicativo no escopo da assinatura
Para atribuir uma função ao aplicativo no escopo da assinatura, use:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

O exemplo a seguir concede a função *Colaborador* para um aplicativo *AD do Azure* na assinatura selecionada.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Atribuir função ao usuário no escopo do grupo de recursos
Para atribuir uma função ao usuário no escopo do grupo de recursos, use:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

O exemplo a seguir concede a função *Colaborador de Máquina Virtual* ao usuário **samert@aaddemo.com* no escopo do grupo de recursos *Pharma-Sales-ProjectForcast*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Atribuir função ao grupo no escopo de recursos
Para atribuir uma função ao grupo no escopo de recursos, use:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

O exemplo a seguir concede a função *Colaborador de Máquina Virtual* para um grupo *AD do Azure* em uma *sub-rede*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Remover acesso
Para remover uma atribuição de função, use:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

O exemplo a seguir remove a atribuição da função *Colaborador de Máquina Virtual* do **sammert@aaddemo.com* no grupo de recursos *Pharma-Sales-ProjectForcast*. Em seguida, ele remove a atribuição de função de um grupo na assinatura.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Criar uma função personalizada
Para criar uma função personalizada, use o comando `azure role create`.

O exemplo a seguir cria uma função personalizada chamada *Operador de Máquina Virtual* que concede acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede acesso para iniciar, reiniciar e monitorar máquinas virtuais. A função personalizada que pode ser usada em duas assinaturas. Este exemplo utiliza um arquivo json como entrada.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role create-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modificar uma função personalizada

Para modificar uma função personalizada, use o comando show da função do Azure para recuperar a definição da função. Em seguida, faça as alterações desejadas na definição de função. Por fim, use o comando set da função do Azure para salvar a definição da função modificada.

O exemplo a seguir adiciona a operação Microsoft.Insights/diagnosticSettings/* para as Ações e uma assinatura do Azure para o AssignableScopes da função personalizada Operador de Máquina Virtual.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Excluir uma função personalizada

Para excluir uma função personalizada, primeiro use o comando `azure role show` para determinar a **Id** da função. Em seguida, use o comando `azure role delete` para excluir a função especificando a **Id**.

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Listar funções personalizadas

Para listar as funções disponíveis para atribuição em um escopo, use o comando `azure role list`.

O exemplo a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

No exemplo a seguir, a função personalizada *Operador de Máquina Virtual* não está disponível na assinatura *Production4*, pois essa assinatura não está no **AssignableScopes** da função.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Tópicos RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->