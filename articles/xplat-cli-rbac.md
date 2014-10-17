<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="mohisri" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Gerenciar o controle de acesso com base em função com interface de linha de plataforma cruzada

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/en-us/documentation/articles/xplat-cli-rbac.md" title="CLI entre plataformas">CLI entre plataformas</a></div>

O controle de acesso com base em função (RBAC) no portal de visualização do Azure e na API do Gerenciador de Recursos do Azure permite que você gerencie o acesso a sua assinatura em um nível detalhado. Com este recurso, você pode conceder acesso aos principais usuários, grupos ou serviços do active directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar a interface de linha de comando de plataforma cruzada do Azure (xplat-cli) para gerenciar o RBAC. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos

Para poder usar o xplat-cli para gerenciar o RBAC, você deve ter o seguinte:

-   A interface de linha de comando da plataforma cruzada do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure][Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure].
-   Leia também os tutoriais a seguir para se familiarizar com a instalação e para usar o Gerenciador de Recursos do Azure na interface de linha de comando da plataforma cruzada do Azure: [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]

## Neste tutorial

-   [Conecte-se as suas assinaturas][Conecte-se as suas assinaturas]
-   [Verifique as atribuições de função existentes][Verifique as atribuições de função existentes]
-   [Crie uma atribuição de função][Crie uma atribuição de função]
-   [Verifique as permissões][Verifique as permissões]
-   [Próximas etapas][Próximas etapas]

## <span id="connect"></span></a>Conecte-se as suas assinaturas

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira coisa a fazer é alternar para o modo do Gerenciador de Recursos do Azure, digite:

    azure config mode arm

Para obter mais informações, consulte [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos].

Para se conectar as suas assinaturas do Azure, digite:

    azure login -u <username>

No prompt da linha de comando, insira sua senha da conta do Azure (somente a ID organizacional de suporte). O Xplat-cli obterá todas as assinaturas que você tem com esta conta e deduzirá por si próprio a usar a primeira como padrão. Observe que com o RBAC, você poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função.

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Para obter mais informações, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure][Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure].

## <span id="check"></span></a>Verifique as atribuições de função existentes

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    azure role assignment list

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1.  Você precisará ter acesso de leitura em nível de assinatura.
2.  Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    azure role assignment list -g group1 --mail <user's email> -o Owner

Isso retornará todas as atribuições de função para um usuário em particular no seu locatário do AD, que tem uma atribuição de função de “Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1.  Uma atribuição de função de “Proprietário" ao usuário para o grupo de recursos.
2.  Uma atribuição de função de “Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado nível, você terá as mesmas permissões que seus filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## <span id="create"></span></a>Crie uma atribuição de função

Para criar uma atribuição de função, você precisa pensar sobre

-   Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do Azure Active Directory para ver quais usuários, grupos ou entidades de serviço você tem no seu locatário do AD.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

-   Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    `azure role list`

-   Qual escopo você deseja atribuir: você tem três níveis para escopos

    -   A assinatura atual
    -   Um grupo de recursos, para obter uma lista dos grupos de recursos, digite `azure group list`
    -   Um recurso, para obter uma lista de recursos, digite `azure resource list`

Use `azure role assignment create` para criar uma atribuição de função. Por exemplo:

-   Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor.

    `azure role assignment create --mail <user's email> -o Reader`

-   Isso criará uma atribuição de função em nível de grupo de recursos

    `PS C:PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`gt; azure role assignment create --mail \<user's email\> -o Contributor -g group1</code>

-   Isso criará uma atribuição de função em nível de recursos

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>Verifique as permissões

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando

    PS C:\> azure group list
    PS C:\> azure resource list

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos onde você tem permissão de leitura. E também mostrará as permissões que você tem.

Então quando você tenta executar outro cmdlet como `azure group create`, você receberá um erro de acesso negado se não tiver a permissão.

## <span id="next"></span></a>Próximas etapas

Para saber mais sobre como gerenciar o controle de acesso com base em função com o xplat-cli e tópicos relacionados:

-   [Instalar e configurar a interface de linha de comando entre plataformas do Azure][Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure]
-   [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]
-   [Usando grupos de recursos para gerenciar os recursos do Azure][Usando grupos de recursos para gerenciar os recursos do Azure]: Saiba como criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
-   [Blog do Azure][Blog do Azure]: Saiba sobre os novos recursos do Azure.

  [Windows PowerShell]: /en-us/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [CLI entre plataformas]: /en-us/documentation/articles/xplat-cli-rbac.md "CLI entre plataformas"
  [Instalar e configurar a interface de linha de comando da plataforma cruzada do Azure]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/
  [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/
  [Conecte-se as suas assinaturas]: #connect
  [Verifique as atribuições de função existentes]: #check
  [Crie uma atribuição de função]: #create
  [Verifique as permissões]: #verify
  [Próximas etapas]: #next
  [Usando grupos de recursos para gerenciar os recursos do Azure]: http://azure.microsoft.com/en-us/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog do Azure]: http://blogs.msdn.com/windowsazure
