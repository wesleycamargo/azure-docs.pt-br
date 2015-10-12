<properties
	pageTitle="Gerenciando o controle de acesso baseado em função com a interface de linha de comando do Azure"
	description="Gerenciando o controle de acesso baseado em função com a interface de linha de comando do Azure"
	services="azure-portal"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="azure-portal"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="inhenk"/>

# Gerenciando o controle de acesso baseado em função com a interface de linha de comando do Azure (CLI do Azure) #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](/role-based-access-control-xplat-cli-install.md)

O controle de acesso com base em função (RBAC) no Portal do Azure e na API do Gerenciador de Recursos do Azure permite que você gerencie o acesso a sua assinatura e recursos em um nível detalhado. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar a CLI do Azure para gerenciar o controle de acesso baseado em função. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos ##

Para poder usar a CLI do AZURE para gerenciar o RBAC, você deve ter o seguinte:

- CLI do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).
- Leia também os tutoriais a seguir para se familiarizar com a configuração e uso do Gerenciador de Recursos do Azure na CLI do AZURE: [Usando a CLI do Azure com o Gerenciamento de Recursos](../xplat-cli-azure-resource-manager.md)

## Neste tutorial ##

* [Conectar-se às suas assinaturas](#connect)
* [Verifique as atribuições de função existente](#check)
* [Criar uma atribuição de função](#create)
* [Verificar permissões](#verify)
* [Próximas etapas](#next)

## <a id="connect"></a>Conectar-se a suas assinaturas ##

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira medida é alternar para o modo do Gerenciador de Recursos do Azure. Digite:

    azure config mode arm

Para obter mais informações, consulte [Usando a CLI do Azure com o Gerenciamento de Recursos](../xplat-cli-azure-resource-manager.md)

Para se conectar às assinaturas do Azure, digite:

    azure login -u <username>

No prompt da linha de comando, insira sua senha da conta do Azure (somente use uma conta organizacional). A CLI do Azure obterá todas as assinaturas que você tem com esta conta e configurará sozinho para usar a primeira como padrão. Observe que com o controle de acesso baseado na função, você somente poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função.

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Para obter mais informações, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

## <a id="check"></a>Verifique as atribuições de função existente ##

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    azure role assignment list

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1. Você precisará ter acesso de leitura em nível de assinatura.
2. Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas elas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    azure role assignment list -g group1 --upn <user email> -o Owner

Isso retornará todas as atribuições de função para um determinado usuário no deu diretório Azure AD, que tem uma atribuição de função de “Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1. Uma atribuição de função de “Proprietário" ao usuário para o grupo de recursos.
2. Uma atribuição de função de “Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado recurso pai, você terá as mesmas permissões para todos os seus recursos filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## <a id="create"></a>Criar uma atribuição de função ##

Para criar uma atribuição de função, você precisa pensar sobre:

- Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do Active Directory do Azure para ver quais usuários, grupos e entidades de serviço tem no seu diretório

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    `azure role list`

- Qual o escopo que você deseja atribuir: você tem três níveis de escopo:

    - A assinatura atual
    - Um grupo de recursos. Para obter uma lista dos grupos de recursos, digite `azure group list`
    - Um recurso. Para obter um lista de recursos, digite `azure resource list`

Em seguida, use `azure role assignment create` para criar uma atribuição de função. Por exemplo:

 - Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor:

    `azure role assignment create --upn <user's email> -o Reader`

- Isso criará uma atribuição de função em um nível de grupo de recursos:

    `PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

- Isso criará uma atribuição de função em um nível de recursos:

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Verificar permissões ##

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando:

    PS C:\> azure group list
    PS C:\> azure resource list

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos onde você tem permissão de leitura. E também mostrará as permissões que você tem.

Quando você tenta executar outros cmdlets como `azure group create`, receberá um erro de acesso negado se não tiver a permissão.

## <a id="next"></a>Próximas etapas ##

Para saber mais sobre como gerenciar o controle de acesso com base em função com o CLI do Azure e tópicos relacionados:

- [Controle de acesso baseado em função no Azure](../role-based-access-control-configure.md)
- [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).
- [Usando a CLI do Azure com o Gerenciamento de Recursos](../xplat-cli-azure-resource-manager.md)
- [Usando grupos de recursos para gerenciar os recursos do Azure](../azure-preview-portal-using-resource-groups.md): aprenda a criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): obtenha informações sobre os novos recursos no Azure.
- [Configurar o controle de acesso com base em função usando o Windows PowerShell](role-based-access-control-powershell.md)
- [Solucionar problemas do controle de acesso com base em função](role-based-access-control-troubleshooting.md)

<!---HONumber=Oct15_HO1-->