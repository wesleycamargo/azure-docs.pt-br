<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Gerenciar o controle de acesso com base em função com o Windows PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/pt-br/documentation/articles/xplat-cli-rbac.md" title="CLI entre plataformas">CLI entre plataformas</a></div>

O controle de acesso com base em função (RBAC) no portal de visualização do Azure e na API do Gerenciador de Recursos do Azure permite que você gerencie o acesso a sua assinatura em um nível detalhado. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Neste tutorial, você aprenderá a usar o Windows PowerShell para gerenciar o RBCA. Este tutorial traz orientações passo a passo do processo de criação e verificação das atribuições de função.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos

Para poder usar o Windows PowerShell para gerenciar o RBAC, você deve ter o seguinte:

-   Windows PowerShell, Versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0][Windows Management Framework 3.0] ou [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   PowerShell do Azure, versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Windows Azure (a página pode estar em inglês)][Como instalar e configurar o PowerShell do Windows Azure (a página pode estar em inglês)].

Este tutorial foi criado para iniciantes do Windows PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)][Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)].

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

    Get-Help Add-AzureAccount -Detailed

Leia também os tutoriais a seguir para se familiarizar com a instalação e para usar o Gerenciador de Recursos do Azure do Windows PowerShell:

-   [Como instalar e configurar o PowerShell do Azure][Como instalar e configurar o PowerShell do Azure]
-   [Usando o Windows PowerShell com o Gerenciador de Recursos][Usando o Windows PowerShell com o Gerenciador de Recursos]

## Neste tutorial

-   [Conecte-se as suas assinaturas][Conecte-se as suas assinaturas]
-   [Verifique as atribuições de função existentes][Verifique as atribuições de função existentes]
-   [Crie uma atribuição de função][Crie uma atribuição de função]
-   [Verifique as permissões][Verifique as permissões]
-   [Próximas etapas][Próximas etapas]

## <span id="connect"></span></a>Conecte-se as suas assinaturas

Como o RBAC somente funciona com o Gerenciador de Recursos do Azure, a primeira coisa a fazer é alternar para o modo do Gerenciador de Recursos do Azure, digite:

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManagergt; Switch-AzureMode -Name AzureResourceManager

Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos][Usando o Windows PowerShell com o Gerenciador de Recursos].

Para se conectar as suas assinaturas do Azure, digite:

    PS C:PS C:\> Add-AzureAccountgt; Add-AzureAccount

No controle do navegador pop-up, insira o nome de usuário e senha da conta do Azure. O PowerShell obterá todas as assinaturas que você tem com esta conta e considerará que o PowerShell usará a primeira como padrão. Observe que com o RBAC, você poderá obter estas assinaturas onde você tem algumas permissões sendo o coadministrador ou tendo alguma atribuição de função.

Se você tem várias assinaturas e deseja alternar para outra, digite:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Para obter mais informações, consulte [Como instalar e configurar o PowerShell do Azure][Como instalar e configurar o PowerShell do Azure].

## <span id="check"></span></a>Verifique as atribuições de função existentes

Agora vamos verificar quais atribuições de função já existem na assinatura. Tipo:

    PS C:PS C:\> Get-AzureRoleAssignmentgt; Get-AzureRoleAssignment

Isso retornará todas as atribuições de função na assinatura. Há duas coisas a serem observadas:

1.  Você precisará ter acesso de leitura em nível de assinatura.
2.  Se a assinatura tiver várias atribuições de função, pode demorar um pouco para obter todas.

Você também pode verificar as atribuições de função existentes para uma definição de função em particular, em um determinado escopo para um usuário específico. Tipo:

    PS C:PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Ownergt; Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Isso retornará todas as atribuições de função para um usuário em particular no seu locatário do AD, que tem uma atribuição de função de “Proprietário" para o grupo de recursos "group1". A atribuição de função pode vir de dois lugares:

1.  Uma atribuição de função de “Proprietário" ao usuário para o grupo de recursos.
2.  Uma atribuição de função de “Proprietário" ao usuário para o pai do grupo de recursos (neste caso, a assinatura), pois se você tiver qualquer permissão em um determinado nível, você terá as mesmas permissões que seus filhos.

Todos os parâmetros deste cmdlet são opcionais. Você pode combiná-los para verificar as atribuições de função com diferentes filtros.

## <span id="create"></span></a>Crie uma atribuição de função

Para criar uma atribuição de função, você precisa pensar sobre

-   Para quem você deseja atribuir a função: você pode usar os seguintes cmdlets do Azure Active Directory para ver quais usuários, grupos ou entidades de serviço você tem no seu locatário do AD.

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

-   Qual função você deseja atribuir: você pode usar o seguinte cmdlet para ver as definições de função com suporte.

    `PS C:\> Get-AzureRoleDefinition`

-   Qual escopo você deseja atribuir: você tem três níveis para escopos

    -   A assinatura atual
    -   Um grupo de recursos, para obter uma lista dos grupos de recursos, digite `PS C:PS C:\> Get-AzureResourceGroup`</code>
    -   Um recurso, para obter uma lista de recursos, digite `PS C:PS C:\> Get-AzureResource`</code>

Use `New-AzureRoleAssignment` para criar uma atribuição de função. Por exemplo:

-   Isso criará uma atribuição de função em nível da assinatura atual para um usuário como um leitor.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

-   Isso criará uma atribuição de função em nível de grupo de recursos

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

-   Isso criará uma atribuição de função em nível de recursos

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <span id="verify"></span></a>Verifique as permissões

Depois de você verificar que sua conta tem algumas atribuições de função, você pode realmente ver as permissões destas atribuições de função concedidas a você executando

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Esses dois cmdlets retornarão apenas os grupos de recursos ou recursos onde você tem permissão de leitura. E também mostrará as permissões que você tem.

Quando você tenta executar outro cmdlet como `New-AzureResourceGroup`, receberá um erro de acesso negado se não tiver a permissão.

## <span id="next"></span></a>Próximas etapas

Para saber mais sobre como gerenciar o controle de acesso com base em função com o Windows PowerShell e tópicos relacionados:

-   [Controle de acesso com base em função do Windows Azure][Controle de acesso com base em função do Windows Azure]
-   [Cmdlets do Gerenciador de Recursos do Azure][Cmdlets do Gerenciador de Recursos do Azure]: Saiba usar os cmdlets nos módulos AzureResourceManager.
-   [Usando grupos de recursos para gerenciar os recursos do Azure][Usando grupos de recursos para gerenciar os recursos do Azure]: Saiba como criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
-   [Blog do Azure][Blog do Azure]: Saiba sobre os novos recursos do Azure.
-   [Blog do Windows PowerShell][Blog do Windows PowerShell]: Saiba mais sobre os novos recursos do Windows PowerShell.
-   [Blog "Hey, Scripting Guy!" Blog][Blog "Hey, Scripting Guy!" Blog]: Obtenha dicas reais e truques da comunidade do Windows PowerShell.
-   [Configurar o controle de acesso com base em função usando o XPLAT CLI][Configurar o controle de acesso com base em função usando o XPLAT CLI]
-   [Solucionar problemas de controle de acesso com base em função][Solucionar problemas de controle de acesso com base em função]

  [Windows Management Framework 3.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=40855
  [Como instalar e configurar o PowerShell do Windows Azure (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/
  [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)]: http://technet.microsoft.com/pt-br/library/hh857337.aspx
  [Como instalar e configurar o PowerShell do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
  [Usando o Windows PowerShell com o Gerenciador de Recursos]: http://azure.microsoft.com/pt-br/documentation/articles/powershell-azure-resource-manager/
  [Conecte-se as suas assinaturas]: #connect
  [Verifique as atribuições de função existentes]: #check
  [Crie uma atribuição de função]: #create
  [Verifique as permissões]: #verify
  [Próximas etapas]: #next
  [Controle de acesso com base em função do Windows Azure]: http://azure.microsoft.com/pt-br/documentation/articles/role-based-access-control-configure/
  [Cmdlets do Gerenciador de Recursos do Azure]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [Usando grupos de recursos para gerenciar os recursos do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog do Azure]: http://blogs.msdn.com/windowsazure
  [Blog do Windows PowerShell]: http://blogs.msdn.com/powershell
  [Blog "Hey, Scripting Guy!" Blog]: http://blogs.technet.com/b/heyscriptingguy/
  [Configurar o controle de acesso com base em função usando o XPLAT CLI]: http://azure.microsoft.com/pt-br/documentation/articles/role-based-access-control-xplat-cli/
  [Solucionar problemas de controle de acesso com base em função]: http://azure.microsoft.com/pt-br/documentation/articles/role-based-access-control-troubleshooting/
