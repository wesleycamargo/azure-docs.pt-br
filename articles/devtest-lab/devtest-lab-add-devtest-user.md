<properties
	pageTitle="Adicionar usuários e proprietários aos Azure DevTest Labs | Microsoft Azure"
	description="Adicionar usuários e proprietários aos Azure DevTest Labs usando o portal do Azure ou o PowerShell"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Adicionar usuários e proprietários aos Azure DevTest Labs

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

O acesso a Azure DevTest Labs é controlado pelo [RBAC (Controle de Acesso Baseado em Função do Azure)](../active-directory/role-based-access-control-what-is.md). Com o RBAC, você pode separar as tarefas de sua equipe em *funções*, onde só concederá o acesso de que os usuários precisam para realizar seus trabalhos. Três dessas funções RBAC são *Proprietário*, *Usuário dos DevTest Labs* e *Colaborador*. Neste artigo, você aprenderá quais ações podem ser executadas em cada uma das três funções RBAC principais. A partir daí, você aprenderá como adicionar usuários a um laboratório - por meio do portal e por meio de um script do PowerShell, além de como adicionar usuários no nível de assinatura.

## Ações que podem ser executadas em cada função

Há três funções principais às quais você pode atribuir um usuário:

- Proprietário
- Usuário do DevTest Labs
- Colaborador

A tabela a seguir ilustra as ações que podem ser executadas por usuários em cada uma dessas funções:

| **Ações que os usuários nesta função podem executar** | **Usuário do DevTest Labs** | **Proprietário** | **Colaborador** |
|---|---|---|---|
| **Tarefas do laboratório** | | | |
| Adicionar usuários a um laboratório | Não | Sim | Não |
| Atualizar configurações de custo | Não | Sim | Sim |
| **Tarefas de base da VM** | | | |
| Adicionar e remover imagens personalizadas | Não | Sim | Sim |
| Adicionar, atualizar e excluir fórmulas | Sim | Sim | Sim |
| Incluir imagens do Azure Marketplace na listra branca | Não | Sim | Sim |
| **Tarefas da VM** | | | |
| Criar VMs | Sim | Sim | Sim |
| Iniciar, parar e excluir VMs | Somente máquinas virtuais criadas pelo usuário | Sim | Sim |
| Atualizar políticas de VM | Não | Sim | Sim |
| Adicionar/remover discos de dados de/para máquinas virtuais | Somente máquinas virtuais criadas pelo usuário | Sim | Sim |
| **Tarefas de artefato** | | | |
| Adicionar e remover repositórios de artefato | Não | Sim | Sim |
| Aplicar artefatos | Sim | Sim | Sim |

> [AZURE.NOTE] Quando um usuário cria uma VM, esse usuário é atribuído automaticamente à função **Proprietário** da VM criada.

## Adicionar um usuário ou proprietário no nível do laboratório

Os proprietários e os usuários podem ser adicionados no nível do laboratório por meio do portal do Azure. Isso inclui usuários externos com uma [Conta da Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account) válida. As etapas a seguir vão orientá-lo durante o processo de adição de um proprietário ou de um usuário a um laboratório nos Azure DevTest Labs:

1. Entre no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços** e selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha do laboratório, selecione **Configuração**.

1. Na folha **Configuração**, selecione **Usuários**.

1. Na folha **Usuários**, selecione **+Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, selecione a função desejada. A seção [Ações que podem ser executadas em cada função](#actions-that-can-be-performed-in-each-role) lista as diversas ações que podem ser executadas por usuários nas funções Proprietário, Usuário de DevTest e Colaborador.

1. Na folha **Adicionar usuários**, insira o endereço de email ou o nome do usuário que você deseja adicionar à função especificada. Se o usuário não for encontrado, uma mensagem de erro explicará o problema. Se o usuário for encontrado, esse usuário será listado e selecionado.

1. Selecione **Selecionar**.

1. Selecione **OK** para fechar a folha **Adicionar acesso**.

1. Quando você retornar para a folha **Usuários**, o usuário terá sido adicionado.

## Adicionar um usuário externo a um laboratório usando o PowerShell

Além de adicionar usuários no portal do Azure, você poderá adicionar um usuário externo ao seu laboratório usando um script do PowerShell. No exemplo a seguir, simplesmente modifique os valores de parâmetro no comentário **Valores a alterar**. Você pode recuperar os valores `subscriptionId`, `labResourceGroup` e `labName` da folha do laboratório no portal do Azure.

> [AZURE.NOTE]
O script de exemplo pressupõe que o usuário especificado tenha sido adicionado como um convidado ao Active Directory e falhará se não for o caso. Para adicionar um usuário que não esteja no Active Directory a um laboratório, use o portal do Azure para atribuir o usuário a uma função conforme ilustrado na seção [Adicionar um proprietário ou usuário no nível do laboratório](#add-an-owner-or-user-at-the-lab-level).

	# Add an external user in DevTest Labs user role to a lab
	# Ensure that guest users can be added to the Azure Active directory:
	# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

	# Values to change
	$subscriptionId = "<Enter Azure subscription ID here>"
	$labResourceGroup = "<Enter lab's resource name here>"
	$labName = "<Enter lab name here>"
	$userDisplayName = "<Enter user's display name here>"

	# Log into your Azure account
	Login-AzureRmAccount
	
	# Select the Azure subscription that contains the lab. 
	# This step is optional if you have only one subscription.
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	# Retrieve the user object
	$adObject = Get-AzureRmADUser -SearchString $userDisplayName
	
	# Create the role assignment. 
	$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
	New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## Adicionar um usuário ou proprietário no nível da assinatura

As permissões do Azure são propagadas do escopo pai para o escopo filho no Azure. Portanto, os proprietários de uma assinatura do Azure que contenha laboratórios automaticamente serão proprietários desses laboratórios. Eles também possuem as máquinas virtuais e outros recursos criados por usuários do laboratório, além do serviço Azure DevTest Labs.

Você pode adicionar outros proprietários a um laboratório por meio da folha do laboratório no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). No entanto, o escopo de administração do proprietário adicionado é mais restrito do que o do proprietário da assinatura. Por exemplo, os proprietários adicionados não têm acesso total a alguns dos recursos criados na assinatura pelo serviço DevTest Labs.

Para adicionar um proprietário a uma assinatura do Azure, siga estas etapas:

1. Entre no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços** e selecione **Assinaturas** na lista.

1. Selecione a assinatura desejada.

1. Selecione o ícone **Acesso**.

	![Usuários do Access](./media/devtest-lab-add-devtest-user/access-users.png)

1. Na folha **Usuários**, selecione **Adicionar**.

	![Adicionar usuário](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Na folha **Selecionar uma função**, selecione **Proprietário**.

1. Na folha **Adicionar usuários**, insira o endereço de email ou o nome do usuário que deseja adicionar como um proprietário. Se não for possível encontrar o usuário, você obterá uma mensagem de erro explicando o problema. Se o usuário for encontrado, ele será listado na caixa de texto **Usuário**.

1. Selecione o nome de usuário localizado.

1. Selecione **Selecionar**.

1. Selecione **OK** para fechar a folha **Adicionar acesso**.

1. Quando você retornar à folha **Usuários**, o usuário terá sido adicionado como proprietário. Agora, esse usuário é o proprietário de todos os laboratórios criados nessa assinatura e, portanto, é capaz de executar tarefas de proprietário.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<!---HONumber=AcomDC_0914_2016-->