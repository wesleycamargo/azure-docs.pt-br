<properties
	pageTitle="Gerenciar função com base no Controle de Acesso (RBAC) com o CLI do Azure | Microsoft Azure"
	description="Saiba como gerenciar o acesso baseado em função (RBAC) com a interface de linha de comando do Azure ao relacionar as funções e ações de função, atribuir funções às assinaturas e escopos de aplicativo."
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

# Gerenciar o Controle de Acesso baseado em função com a Interface de Linha de Comando do Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

O RBAC (Controle de Acesso baseado em função) no Portal do Azure e na API do Azure Resource Manager permite gerenciar o acesso a sua assinatura e aos recursos de maneira detalhada. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Antes de poder usar a CLI do Azure para gerenciar o RBAC, é necessário ter o seguinte:

- CLI do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).
- Azure Resource Manager na Azure CLI. Acesse [Usando a Azure CLI com o Resource Manager](../xplat-cli-azure-resource-manager.md) para obter mais detalhes.

## Listar funções

###	Relacionar todas as funções disponíveis
Para relacionar todas as funções disponíveis, use:

		azure role list

O exemplo a seguir mostra a relação de *todas as funções disponíveis*.

![Linha de comando do Azure RBAC - lista de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Relacionar ações de uma função
Para relacionar as ações de uma função, use:

    azure role show <role in quotes>

O exemplo a seguir mostra as ações do *Colaborador* e as funções do *Colaborador da máquina virtual*.

![Linha de comando do Azure RBAC - exibição de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Relacionar acesso
###	Relacionar as atribuições de função como efetivas em um grupo de recursos
Para listar as atribuições de função que existem em um grupo de recursos, use:

    azure role assignment list --resource-group <resource group name>

O exemplo a seguir mostra as atribuições de função no grupo *pharma-sales-projecforcast*.

![Linha de comando do Azure RBAC - lista de atribuição de funções do azure por grupo - captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Relacionar atribuições de função a um usuário, incluindo os que são atribuídos a um grupo de usuários

O exemplo a seguir mostra as atribuições de função concedidas ao usuário **sameert@aaddemo.com*. Isso inclui funções atribuídas diretamente ao usuário, mas também funções herdadas de grupos.

![Linha de comando do Azure RBAC - lista de atribuição de funções do azure por usuário - captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Conceder acesso
Depois de ter identificado a função que você deseja atribuir para conceder acesso, use:

    azure role assignment create

###	Atribuir função ao grupo no escopo da assinatura
Para atribuir função ao grupo no escopo da assinatura, use:

	azure role assignment create --objId  <group's object id> --role <name of role> --scope <subscription/subscription id>

O exemplo a seguir atribui a função *Leitor* para a *equipe de Christine Koch* no escopo da *assinatura*.

![Linha de comando do Azure RBAC - criação de atribuição de funções do azure por grupo - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Atribuir função ao aplicativo no escopo da assinatura
Para atribuir uma função ao aplicativo no escopo da assinatura, use:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

O exemplo a seguir concede a função *Colaborador* para um aplicativo *AD do Azure* na assinatura selecionada.

 ![Linha de comando do Azure RBAC - criação de atribuição de funções do azure por aplicativo](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Atribuir função ao usuário no escopo do grupo de recursos
Para atribuir uma função ao usuário no escopo do grupo de recursos, use:

	azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

O exemplo a seguir concede a função *Colaborador de Máquina Virtual* ao usuário **samert@aaddemo.com* no escopo do grupo de recursos *Pharma-Sales-ProjectForcast*.

![Linha de comando do Azure RBAC - criação de atribuição de funções do azure por usuário - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Atribuir função ao grupo no escopo de recursos
Para atribuir uma função ao grupo no escopo de recursos, use:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

O exemplo a seguir concede a função *Colaborador de Máquina Virtual* para um grupo *AD do Azure* em uma *sub-rede*.

![Linha de comando do Azure RBAC - criação de atribuição de funções do azure por grupo - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Remover acesso
Para remover uma atribuição de função, use:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

O exemplo a seguir remove a atribuição da função *Colaborador de Máquina Virtual* do **sammert@aaddemo.com* no grupo de recursos *Pharma-Sales-ProjectForcast*. Em seguida, ele remove a atribuição de função de um grupo na assinatura.

![Linha de comando do Azure RBAC - exclusão de atribuição de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Criar uma função personalizada
Para criar uma função personalizada, use o comando `azure role create`.

O exemplo a seguir cria uma função personalizada chamada *Operador de Máquina Virtual* que concede acesso a todas as operações de leitura dos provedores de recurso *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede acesso para iniciar, reiniciar e monitorar máquinas virtuais. A função personalizada que pode ser usada em duas assinaturas. Este exemplo utiliza um arquivo json como entrada.

![JSON - definição de função personalizada - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Linha de comando do Azure RBAC - criação de função do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modificar uma função personalizada

Para modificar uma função personalizada, use o comando `azure role show` para recuperar a definição da função. Em seguida, faça as alterações desejadas à definição de função. Por fim, use `azure role set` para salvar a definição de função modificada.

O exemplo a seguir adiciona a operação Microsoft.Insights/diagnosticSettings/* às **Ações**, e uma assinatura do Azure a **AssignableScopes** da função personalizada do Operador de Máquina Virtual.

![JSON - modificar definição de função personalizada - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Linha de comando do Azure RBAC - conjunto de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Excluir uma função personalizada

Para excluir uma função personalizada, primeiro use o comando `azure role show` para determinar a **Id** da função. Em seguida, use o comando `azure role delete` para excluir a função especificando a **Id**.

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual*.

![Linha de comando do Azure RBAC - exclusão de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Listar funções personalizadas

Para listar as funções disponíveis para atribuição em um escopo, use o comando `azure role list`.

O exemplo a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

![Linha de comando do Azure RBAC - lista de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

No exemplo a seguir, a função personalizada *Operador de Máquina Virtual* não está disponível na assinatura *Production4*, pois essa assinatura não está no **AssignableScopes** da função.

![Linha de comando do Azure RBAC - lista de funções do azure para funções personalizadas - captura de tela](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Tópicos RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->