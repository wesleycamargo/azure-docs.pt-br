    <properties
	pageTitle="Grant user permissions to specific DevTest Lab policies | Microsoft Azure"
	description="Learn how to grant user permissions to specific DevTest Lab policies based on each user's needs"
	services="devtest-lab,virtual-machines,visual-studio-online"
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
	ms.date="02/09/2016"
	ms.author="tarcher"/>

# Conceder permissões de usuário para políticas específicas do laboratório de Desenvolvimento/Teste

## Visão geral

Este artigo ilustra como usar o PowerShell para conceder aos usuários permissões para uma determinada política de laboratório de Desenvolvimento/Teste do Azure. Dessa forma, as permissões podem ser aplicadas com base nas necessidades de cada usuário. Por exemplo, talvez você queira conceder a um usuário específico a capacidade de alterar as configurações de política de VM, mas não as políticas de custo.

## Políticas como recursos

Como discutido no artigo [Controle de acesso baseado em função do Azure](/role-based-access-control-configure.md), o RBAC permite o gerenciamento de acesso refinado de recursos do Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe de operação de desenvolvimento e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

No laboratório de Desenvolvimento/Teste, uma política é um tipo de recurso que habilita a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política do laboratório de Desenvolvimento/Teste é um recurso do tipo de recurso Política e pode ser atribuída como um escopo a uma função RBAC.

Por exemplo, para conceder aos usuários permissão de leitura/gravação para a política **Tamanhos de VM permitidos**, você deve criar uma função personalizada que funciona com a ação **Microsoft.DevTestLab/labs/policySets/policies/*** e, em seguida, atribuir os usuários adequados para esta função personalizada no escopo do **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, consulte a seção [Funções personalizadas no RBAC do Azure](/role-based-access-control-configure.md#custom-roles-in-azure-rbac) do artigo [Controle de acesso baseado em funções do Azure](/role-based-access-control-configure.md) artigo.

##Criar uma função personalizada do laboratório de Desenvolvimento/Teste usando o PowerShell
Para começar, você precisará ler o seguinte artigo, que explica como instalar e configurar os cmdlets do Azure PowerShell: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Depois de configurar os cmdlets do Azure PowerShell, você pode executar as seguintes tarefas:

- Listar todas as operações/ações para um provedor de recursos
- Listar ações em uma função específica:
- Criar uma função personalizada

O seguinte script do PowerShell apresenta exemplos de como executar essas tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##Atribuir permissões a um usuário para uma política específica usando funções personalizadas
Depois de definir suas funções personalizadas, você pode atribuí-las aos usuários. Para atribuir uma função personalizada a um usuário, você deve primeiro obter o **ObjectId** que representa esse usuário. Para fazer isso, use o cmdlet **Get-AzureRmADUser**.

No exemplo a seguir, o **ObjectId** do usuário *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Depois que tiver o **ObjectId** para o usuário e um nome de função personalizado, você pode atribuir essa função ao usuário com o cmdlet **New-AzureRmRoleAssignment**:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

No exemplo anterior, a política **AllowedVmSizesInLab** é usada. Você pode usar qualquer uma das políticas a seguir:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

## Próximas etapas

Após você tiver concedido permissões de usuário para políticas específicas do laboratório de Desenvolvimento/Teste, estas são algumas das próximas etapas a serem consideradas:

- [Acesso seguro a um Laboratório de Desenvolvimento/Teste](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).

- [Adicionar uma VM com artefatos a um Laboratório de Desenvolvimento/Teste do Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0211_2016-->