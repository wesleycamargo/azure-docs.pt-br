<properties pageTitle="Gerenciando o controle de acesso baseado em função com Interface de linha de comando de plataforma cruzada do Azure"metaKeywords ="ResourceManager, interface de linha de comando entre plataformas do Azure, linha de comando do Azure, linha de comando do azure, azure cli, RBAC" description="Gerenciando o controle de acesso baseado em função com a interface de linha de comando entre plataformas" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Gerenciar o controle de acesso baseado em função com interface de linha de comando de plataforma cruzada #

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">CLI de plataforma cruzada do</a><a href="/pt-br/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">Windows PowerShell</a></div>

O controle de acesso baseado em função (RBAC) no portal de visualização do Azure e na API do Gerenciador de Recursos do Azure permite que você gerencie o acesso a sua assinatura em um nível detalhado. Com este recurso, você pode conceder acesso aos principais usuários, grupos ou serviços do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar a interface de linha de comando de plataforma cruzada do Azure (xplat-cli) para gerenciar o RBAC. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos ##

Para poder usar o xplat-cli para gerenciar o RBAC, você deve ter o seguinte:

- 	A interface de linha de comando da plataforma cruzada do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/).
- 	Leia também os tutoriais a seguir para se familiarizar com a instalação e para usar o Gerenciador de Recursos na interface de linha de comando da plataforma cruzada do Azure: [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli-azure-resource-manager/)

## Neste tutorial ##

* [Conecte-se as suas assinaturas](#connect)
* [Verifique as atribuições de função existentes](#check)
* [Crie uma atribuição de função](#create)
* [Verifique as permissões](#verify)
* [Próximas etapas](#next)

## <a id="connect"></a>Conecte-se às suas assinaturas ##

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira coisa a fazer é alternar para o modo do Gerenciador de Recursos do Azure, digite:

    braço do modo de configuração do Azure

Para obter mais informações, consulte [Usando a interface de linha de comando da plataforma cruzada do Azure com o Gerenciador de Recursos](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli-azure-resource-manager/)

Para se conectar as suas assinaturas do Azure, digite:

    logon do Azure -u <username>

No prompt da linha de comando, insira sua senha da conta do Azure (somente a ID organizacional de suporte). O Xplat-cli obterá todas as assinaturas que você tem com esta conta e deduzirá por si próprio a usar a primeira como padrão. Observe que com o RBAC, você poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função. 

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # Isso mostrará as assinaturas na conta.
    lista de contas do azure
    # Use o nome da assinatura para selecionar aquele que você deseja trabalhar.
    conjunto de conta do Azure <nome da assinatura>

Para obter mais informações, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/).

## <a id="check"></a>Verifique as atribuições de função existentes ##

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    lista de atribuição de função do Azure

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1. Você precisará ter acesso de leitura em nível de assinatura.
2. Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    lista de atribuição de função do Azure -g group1 --mail <user's email> -o Owner

Isso retornará todas as atribuições de função para um usuário em particular no seu locatário do AD, que tem uma atribuição de função de "Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1. Uma atribuição de função de "Proprietário" ao usuário para o grupo de recursos.
2. Uma atribuição de função de "Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado nível, você terá as mesmas permissões que seus filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## <a id="create"></a>Crie uma atribuição de função ##

Para criar uma atribuição de função, você precisa pensar sobre

- Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do Active Directory do Azure para ver quais usuários, grupos ou entidades de serviço você tem no seu locatário do AD.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    `azure role list`

- Qual escopo você deseja atribuir: você tem três níveis para escopos

    - A assinatura atual
    - Um grupo de recursos, para obter uma lista dos grupos de recursos, digite `lista de grupo do Azure`
    - Um recurso, para obter uma lista de recursos, digite 'lista de recursos do Azure'

Use 'criar atribuições de funções do Azure' para criar uma atribuição de função. Por exemplo:

 - Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor.

    `azure role assignment create --mail <user's email> -o Reader`

- Isso criará uma atribuição de função em nível de grupo de recursos

    `PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`

- Isso criará uma atribuição de função em nível de recursos

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Verifique as permissões ##

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando

    PS C:\> azure group list
    PS C:\> azure resource list

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos aos quais você tem permissão de leitura. E também mostrará as permissões que você tem.

Então, quando você tenta executar outro cmdlet como, `criação de grupo do Azure`, você receberá um erro de acesso negado se não tiver a permissão.

## <a id="next"></a>Próximas etapas ##

Para saber mais sobre como gerenciar o controle de acesso com base em função com o xplat-cli e tópicos relacionados:

- [Instalar e configurar a interface de linha de comando entre plataformas do Azure](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/)
- [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos](http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli-azure-resource-manager/)
- [Usando grupos de recursos para gerenciar os recursos do Azure](http://azure.microsoft.com/pt-br/documentation/articles/azure-preview-portal-using-resource-groups): Saiba como criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): Saiba sobre os novos recursos do Azure.
