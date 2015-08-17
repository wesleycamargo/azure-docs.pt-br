<properties
	pageTitle="Como gerenciar o controle de acesso baseado em função com a CLI do Azure para Mac, Linux e Windows"
	description="Como gerenciar o controle de acesso baseado em função com a CLI do Azure"
	services=""
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tomfitz"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="tomfitz"/>

# Gerenciando o controle de acesso baseado em função com a interface de linha de comando do Azure (CLI do Azure)#

O controle de acesso baseado em função (RBAC) no Portal do Azure e na API de Gerenciamento de Recursos do Azure permite que você gerencie o acesso à sua assinatura em um nível detalhado. Com este recurso, você pode conceder acesso aos principais usuários, grupos ou serviços do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar a CLI do Azure para gerenciar o RBAC. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos

Para poder usar a CLI do AZURE para gerenciar o RBAC, você deve ter o seguinte:

- CLI do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [instalar](xplat-cli-install.md).
- Leia também os tutoriais a seguir para se familiarizar com a configuração e o uso do Gerenciador de Recursos do Azure na CLI do Azure: [Usando a CLI do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md)

## Conectar-se às suas assinaturas 

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira coisa a fazer é alternar para o modo do Gerenciador de Recursos do Azure, digite:

    azure config mode arm

Para obter mais informações, consulte [Usando a CLI do Azure com o Gerenciamento de Recursos](xplat-cli-azure-resource-manager.md)

Para se conectar as suas assinaturas do Azure, digite:

    azure login -u <username>

No prompt da linha de comando, insira sua senha da conta do Azure (somente para trabalho de suporte ou IDs escolares – também chamado de **ID organizacional**). A CLI do Azure obterá todas as assinaturas que você tem com esta conta e descobrirá como usar a primeira como padrão. Observe que com o RBAC, você poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função.

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Para obter mais informações, consulte os [comandos da CLI do Azure](azure-cli-arm-commands.md).

## <a id="check"></a>Verifique as atribuições de função existente ##

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    azure role assignment list

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1. Você precisará ter acesso de leitura em nível de assinatura.
2. Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    azure role assignment list -g group1 --mail <user's email> -o Owner

Isso retornará todas as atribuições de função para um usuário em particular no seu locatário do AD, que tem uma atribuição de função de “Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1. Uma atribuição de função de “Proprietário" ao usuário para o grupo de recursos.
2. Uma atribuição de função de “Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado nível, você terá as mesmas permissões que seus filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## Criar uma atribuição de função 

Para criar uma atribuição de função, você precisa pensar sobre

- Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do active directory do Azure para ver quais usuários, grupos e entidades de serviço tem no seu locatário do AD.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    `azure role list`

- Qual o escopo que você deseja atribuir: você tem três níveis de escopo

    - A assinatura atual
    - Um grupo de recursos, para obter uma lista dos grupos de recursos, digite `azure group list`
    - Um recurso, para obter uma lista de recursos, digite `azure resource list`

Em seguida, use `azure role assignment create` para criar uma atribuição de função. Por exemplo:

 - Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor.

    `azure role assignment create --mail <user's email> -o Reader`

- Isso criará uma atribuição de função em nível de grupo de recursos

    `PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`

- Isso criará uma atribuição de função em nível de recursos

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## Verificar permissões 

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando

    PS C:\> azure group list
    PS C:\> azure resource list

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos onde você tem permissão de leitura. E também mostrará as permissões que você tem.

Então quando você tenta executar outro cmdlet como `azure group create`, você receberá um erro de acesso negado se não tiver a permissão.

## Próximas etapas 

Para saber mais sobre como gerenciar o controle de acesso com base em função com o CLI do Azure e tópicos relacionados:

- [Instalar e configurar a CLI do Azure](xplat-cli-install.md).
- [Usando a CLI do Azure com o Gerenciamento de Recursos](xplat-cli-azure-resource-manager.md)
- [Usando grupos de recursos para gerenciar os recursos do Azure](resource-groups-overview.md): aprenda a criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.

<!---HONumber=August15_HO6-->