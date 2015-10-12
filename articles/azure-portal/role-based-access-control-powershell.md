<properties
	pageTitle="Gerenciar o controle de acesso com base em função com o Windows PowerShell"
	description="Gerenciar o controle de acesso com base em função com o Windows PowerShell"
	services="azure-portal"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor="mollybos"/>

<tags
	ms.service="azure-portal"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="inhenk"/>

# Gerenciar o controle de acesso com base em função com o Windows PowerShell #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli-install.md)


O controle de acesso baseado em função (RBAC) no Portal do Azure e na API de Gerenciamento de Recursos do Azure permite que você gerencie o acesso à sua assinatura em um nível detalhado. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar o Windows PowerShell para gerenciar o RBCA. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos

Para poder usar o Windows PowerShell para gerenciar o RBAC, você deve ter o seguinte:

- Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite: `$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, confira [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- PowerShell do Azure, versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).

Este tutorial foi criado para iniciantes do Windows PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)](http://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

	Get-Help Add-AzureAccount -Detailed

Leia também os tutoriais a seguir para se familiarizar com a instalação e para usar o Gerenciador de Recursos do Azure do Windows PowerShell:

- [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)
- [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md)


## Conectar-se às suas assinaturas

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira coisa a fazer é alternar para o modo do Gerenciador de Recursos do Azure, digite:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Para se conectar as suas assinaturas do Azure, digite:

    PS C:\> Add-AzureAccount

No controle do navegador pop-up, insira o nome de usuário e senha da conta do Azure. O PowerShell obterá todas as assinaturas que você tem com esta conta e considerará que o PowerShell usará a primeira como padrão. Observe que com o RBAC, você poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função.

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Para obter mais informações, consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).

## Verifique as atribuições de função existente

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    PS C:\> Get-AzureRoleAssignment

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1. Você precisará ter acesso de leitura em nível de assinatura.
2. Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Isso retornará todas as atribuições de função para um usuário em particular no seu locatário do AD, que tem uma atribuição de função de “Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1. Uma atribuição de função de “Proprietário" ao usuário para o grupo de recursos.
2. Uma atribuição de função de “Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado nível, você terá as mesmas permissões que seus filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## Criar uma atribuição de função

Para criar uma atribuição de função, você precisa pensar sobre:

Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do active directory do Azure para ver quais usuários, grupos e entidades de serviço tem no seu locatário do AD.

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    PS C:\> Get-AzureRoleDefinition

Qual o escopo que você deseja atribuir: você tem três níveis de escopo

    - The current subscription
    - A resource group, to get a list of resource groups, type `PS C:\> Get-AzureResourceGroup`
    - A resource, to get a list of resources, type `PS C:\> Get-AzureResource`

Em seguida, use `New-AzureRoleAssignment` para criar uma atribuição de função. Por exemplo:


Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor.

	 PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

Isso criará uma atribuição de função em nível de grupo de recursos.

	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

Isso criará uma atribuição de função para um grupo em nível de grupo de recursos.

	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

Isso criará uma atribuição de função em nível de recurso.

	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Verificar permissões

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos onde você tem permissão de leitura. E também mostrará as permissões que você tem.

Então quando você tenta executar outro cmdlet como `New-AzureResourceGroup`, você receberá um erro de acesso negado se não tiver a permissão.

## Próximas etapas

Para saber mais sobre como gerenciar o controle de acesso com base em função com o Windows PowerShell e tópicos relacionados:

- [Controle de acesso baseado em função no Azure](../role-based-access-control-configure.md)
- [Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): saiba usar os cmdlets no módulo AzureResourceManager.
- [Usando grupos de recursos para gerenciar os recursos do Azure](../azure-preview-portal-using-resource-groups.md): aprenda a criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): obtenha informações sobre os novos recursos no Azure.
- [Blog do Windows PowerShell](http://blogs.msdn.com/powershell): obtenha informações sobre os novos recursos do Windows PowerShell.
- [Blog "Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenha dicas reais e truques da comunidade.do Windows PowerShell.
- [Configure o acesso baseado em função usando o CLI do Azure](role-based-access-control-xplat-cli-install.md)
- [Solucionar problemas do controle de acesso com base em função](role-based-access-control-troubleshooting.md)

<!---HONumber=Oct15_HO1-->