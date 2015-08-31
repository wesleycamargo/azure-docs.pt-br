<properties
	pageTitle="Controle de acesso baseado em função no portal do Microsoft Azure"
	description="Descreve como funciona o controle de acesso baseado em função e como configurá-lo"
	services=""
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="Ibiza"
	ms.workload="infrastructure-services"
	ms.date="08/14/2015"
	ms.author="inhenk"/>

# Controle de acesso baseado em função no portal do Microsoft Azure

Adicionamos suporte para o RBAC (controle de acesso com base em função) no portal de Microsoft Azure para ajudar as organizações a atender seus requisitos de gerenciamento de acesso de forma simples e precisa. A [postagem no blog](http://go.microsoft.com/fwlink/?LinkId=511576) fornecerá a você uma introdução rápida do recurso e ajudará você a começar. Este tópico descreve os conceitos em detalhes e abrange casos de uso adicionais.


## RBAC no Azure

Cada assinatura do Azure está associada com um Active Directory do Azure. Os usuários e serviços que acessam os recursos da assinatura usando o Portal de Gerenciamento do Microsoft Azure ou a API de Gerenciador de Recursos do Azure primeiro precisam se autenticar com o Active Directory do Azure.

![][1]

O controle de acesso com base em função permite que você conceda acesso apropriado aos usuários, grupos e serviços do Azure AD, atribuindo funções para eles em uma assinatura ou grupo de recursos ou nível de recurso individual. A função atribuída define o nível de acesso que os usuários, grupos ou serviços tem no recurso do Azure.

### Função

Uma função é uma coleção de ações que podem ser executadas nos recursos do Azure. Um usuário ou um serviço tem a permissão de executar uma ação em um recurso do Azure, se eles forem atribuídos uma função que contém aquela ação. Para obter uma lista de funções internas e **suas** ações e **não** as propriedades das ações, consulte [Funções internas](#builtinroles).

### Atribuição de função

O acesso é concedido aos usuários e serviços do Azure AD atribuindo a função apropriada para eles em um recurso do Azure.

#### Entidades de segurança do Azure AD

As funções podem ser atribuídas para os seguintes tipos de entidades de segurança do Azure AD:

+ **Usuários**: As funções podem ser atribuídas aos usuários organizacionais que estão no Azure AD com os quais a assinatura do Azure está associada. As funções também podem ser atribuídas aos usuários da conta da Microsoft externa (como joe@outlook.com) usando a ação de Convite para atribuir ao usuário um função no portal de visualização do Azure. Ao atribuir uma função a um usuário de conta da Microsoft externa faz com que uma conta de convidado seja criada no Azure AD para ele. Se essa conta de convidado está desabilitada no diretório, o usuário externo não poderá acessar nenhum recurso do Azure para o qual o usuário tem concedido acesso.
+ **Grupos**: as funções podem ser atribuídas para os grupos de segurança do Azure AD. Um usuário é concedido acesso automaticamente a um recurso se ele se tornar um membro de um grupo que tem acesso. O usuário também perde o acesso automaticamente ao recurso após ser removido do grupo. Gerenciar o acesso através de grupos ao atribuir funções aos grupos e adicionar usuários a esses grupos é a prática recomendada, em vez de atribuir funções diretamente aos usuários. O RBAC do Azure não permite atribuir funções a listas de distribuição. A capacidade de atribuir funções a grupos permite que uma organização estenda seu modelo de controle de acesso existente de seu diretório local para a nuvem, para que os grupos de segurança que já estejam estabelecidos para controlar o acesso local possam ser reutilizados no portal de Portal do Azure. Para obter mais informações sobre as diferentes opções para sincronizar usuários e grupos de um diretório local, consulte [Integração de diretórios](http://technet.microsoft.com/library/jj573653.aspx). O Azure AD Premium também oferece um [recurso de gerenciamento de grupos delegados](http://msdn.microsoft.com/library/azure/dn641267.aspx) com o qual a capacidade de criar e gerenciar grupos pode ser delegada aos usuários não administradores do Azure AD.
+ **Entidades de serviço**: as identidades de serviço são representadas como entidades de serviço no diretório. Elas se autenticam com o Azure AD e se comunicam com segurança umas com as outras. Os serviços podem ser concedidos acesso aos recursos do Azure ao atribuir funções através do módulo do Azure para o Windows PowerShell à entidade de serviço do Azure AD representando aquele serviço.

#### Escopo do recurso

O acesso não precisa ser concedido para toda a assinatura. As funções podem ser atribuídas para grupos de recursos bem como para recursos individuais. No Azure RBAC, um recurso herda atribuições de função de seus recursos pai. Então se um usuário, grupo ou serviço é concedido acesso somente a um grupo de recursos dentro da assinatura, eles poderão somente acessar aquele grupo de recursos e os recursos dentro dele, e não outros grupos de recursos dentro da assinatura. Como outro exemplo, um grupo de segurança pode ser adicionado à função de Leitor para um grupo de recursos, mas ser adicionado a uma função de Colaborador para um banco de dados dentro daquele grupo de recursos.

![][2]

## Coexistência do RBAC com os coadministradores de assinatura

Os administradores e coadministradores de assinatura continuarão a ter acesso completo aos portais do Azure e as APIs de gerenciamento. No modelo RBAC, eles recebem a função de Proprietário no nível de assinatura. No entanto, há suporte para o novo modelo RBAC apenas no Portal do Azure e nas APIs do Gerenciador de Recursos do Azure. Os usuários e serviços que são atribuídos às funções RBAC não podem acessar o Portal de Gerenciamento do Azure e as APIs de Gerenciamento de serviços. Ao adicionar um usuário a função de Proprietário de uma assinatura no Portal do Azure não torna aquele usuário um coadministrador da assinatura no portal completo do Azure.

Se você deseja permitir acesso a um usuário para um recurso do Azure que ainda não está disponível para ser gerenciado por meio do Portal de Gerenciamento, você deve adicioná-los aos coadministradores de assinatura usando o portal completo de Gerenciamento do Azure. O Barramento de Serviço e o StorSimple são exemplos de recursos que, atualmente, não podem ser gerenciados usando o RBAC.

## Autorização para o gerenciamento versus operações de dados

O controle de acesso com base em função tem suporte somente para as operações de gerenciamento dos recursos do Azure no Portal do Azure e nas APIs do Gerenciador de Recursos do Azure. Nem todas as operações no nível de dados para os recursos do Azure podem ser autorizadas por meio do RBAC. Por exemplo, a criação/leitura/atualização/exclusão de contas de armazenamento pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de blobs ou tabelas dentro da conta de armazenamento não pode ser controlada por meio do RBAC. Semelhantemente, a criação/leitura/atualização/exclusão de um BD SQL pode ser controlada por meio do RBAC, mas a criação/leitura/atualização/exclusão de tabelas de SQL dentro do BD ainda não pode ser controlada por meio do RBAC.

## Como adicionar e remover acesso

Vamos olhar um exemplo de como o proprietário do recurso em uma organização pode gerenciar o acesso. Nesse cenário, você tem várias pessoas trabalhando em uma variedade de testes e projetos de produção que são desenvolvidos usando os recursos do Azure. Você deseja seguir as práticas recomendadas para conceder acesso. Os usuários devem ter acesso a todos os recursos que eles precisam, porém não acesso adicional. Você deseja reutilizar todos os investimentos que você fez nos processos e ferramentas para usar grupos de segurança que são controlados em um Active Directory local. Estas seções cobrem como configurar o acesso para estes recursos:

* [Adicionar acesso](#add-access)
* [Remover acesso](#remove-access)
* [Adicionar ou remover o acesso para o usuário externo](#add-or-remove-access-for-external-user)

### Adicionar acesso

Aqui está um resumo dos requisitos de acesso e como eles são configurados no Azure.

Usuário/Grupo | Requisitos de acesso | função e escopo para o acesso
------------- | -------------  | ------------
Todas as equipes de Jill Santos | Leia todos os recursos do Azure | Adicione o grupo AD que representa a equipe de Jill Santos para a função de Leitor para a assinatura do Azure.
Todas as equipes de Jill Santos | Crie e gerencie todos os recursos do grupo de recursos Test | Adicione o grupo AD que representa a equipe de Jill Santos para a função de Colaborador para o grupo de recursos Test
Brock | Crie e gerencie todos os recursos do grupo de recursos Prod | Adicione Brock à função de Colaborador para o grupo de recursos Prod


Primeiro, vamos adicionar o acesso à Leitura para todos os recursos da assinatura. Clique em **Procurar > Todos > Assinaturas**.

![][3]

Clique no *nome de sua assinatura* ** > Leitor > Adicionar**. Na lista de usuários e grupos, selecione ou digite o nome do grupo do Active Directory.

![][4]

Depois adicione a mesma equipe à função de colaborador do grupo de recursos de teste. Clique no grupo de recursos para abrir sua lâmina de propriedade. Em **Funções**, clique em **Colaborador > Adicionar** e digite o nome da equipe.

![][5]

Para adicionar Brock à função de Colaborador do grupo de recursos Prod, clique no grupo de recursos, clique em **Colaborador > Adicionar** e digite o nome do Brock.

![][6]

As atribuições de função podem também ser gerenciadas usando o módulo Microsoft Azure para o Windows PowerShell. Aqui está um exemplo de como adicionar a conta de Brock usando o cmdlet New-AzureRoleAssignment em vez do portal:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Para obter mais informações sobre como usar o Windows PowerShell para adicionar e remover o acesso, consulte [Gerenciar o controle de acesso com base em função com o Windows PowerShell](role-based-access-control-powershell.md).

### Remover acesso

Você também pode remover atribuições facilmente. Digamos que você deseja remover um usuário chamado de Brad Adams da função de Leitor para um grupo de recursos chamado TestDB. Abra a lâmina do grupo de recursos, clique em **Leitor > Brad Adams > Remover**.

![][7]

Aqui está um exemplo de como remover Brad Adams usando o cmdlet New-AzureRoleAssignment:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Adicionar ou remover o acesso para o usuário externo

A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções podem ser alteradas apenas na interface do usuário (não há nenhum método de API do Windows PowerShell) no portal do Azure completo por um administrador global do diretório. Para abrir a guia **Configurar** no portal do Azure, clique em **Active Directory** e no nome do diretório.

![][10]

Então você pode editar as opções para controlar o acesso para os usuários externos.

![][8]

Por padrão, os convidados não podem enumerar o conteúdo do diretório, assim eles não podem ver nenhum usuário ou grupo na **Lista de membros**. Eles podem pesquisar por um usuário digitando o endereço de email completo do usuário e depois conceder acesso. O conjunto de restrições padrão para os convidados são:

- Eles não podem enumerar os usuários e grupos no diretório.
- Eles podem ver detalhes limitados de um usuário se sabem o endereço de email do usuário.
- Eles podem ver detalhes limitados de um grupo quando eles sabem o nome do grupo.

A capacidade dos convidados verem detalhes limitados de um usuário ou grupo permite que eles convidem outras pessoas e vejam alguns detalhes das pessoas com quem eles estão colaborando.

Vamos passar pelo processo para adicionar o acesso para um usuário externo. Adicionaremos um usuário externo a mesma função de Leitor para grupo de recursos TestDB para que o usuário possa ajudar a depurar um erro. Abra a lâmina do grupo de recursos, clique em **Leitor > Adicionar > Convidar** e digite o endereço de email do usuário que deseja adicionar.

![][9]

Quando você adiciona um usuário externo, um convidado é criado no diretório. Posteriormente, aquele convidado pode ser adicionado a um grupo ou removido de um grupo, ou você pode adicioná-lo ou removê-lo individualmente de uma função simplesmente como faria para qualquer outro usuário do diretório.

Você pode também remover um convidado de qualquer função, simplesmente como você removeria qualquer usuário. Remover o convidado de uma função em um recurso não remove o convidado do diretório.

## Como controlar alterações nas atribuições de função

As alterações de atribuições de função são registradas nos [Logs de auditoria](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) de forma semelhante a outros eventos. O log de alterações de atribuição de função pode ser recuperado usando o [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) ou a [API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx).

Por exemplo, para recuperar a lista de alterações de atribuição de função de uma assinatura inteira, execute os dois cmdlets a seguir com o Azure PowerShell. O primeiro alterna para o modo do Gerenciador de Recursos do Azure.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

As alterações de atribuição de função são capturadas em eventos nos quais ResourceProviderName é `Microsoft.Authorization`. Os detalhes reais da atribuição são capturados nos detalhes do evento: qual entidade de segurança foi atribuída, a qual função, para qual escopo. As alterações de atribuição de função são visíveis ao navegar pelos logs de auditoria no portal, mas o portal não mostra os detalhes do evento. Para exibir detalhes do evento, você deve usar o Azure PowerShell.

###Detalhes do evento

Aqui está um exemplo de detalhes do evento para uma alteração de atribuição de função:

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

As informações do evento são interpretadas da seguinte maneira:

| Campo | Valor | Detalhes |
| --- | --- | --- |
| Chamador |	`William.Hennum@contoso.com` | A entidade de segurança que fez a atribuição de função. A entidade de segurança pode ser um usuário, um grupo ou uma entidade de serviço.
| Método HttpRequest: | `PUT` | A ação executada. PUT concede uma atribuição e DELETE remove uma atribuição. |
| Propriedades: PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	A ID de objeto da entidade de segurança que foi atribuída à função. A entidade de segurança pode ser um usuário, um grupo ou uma entidade de serviço. Você pode determinar o nome e o tipo da entidade de segurança pesquisando no Active Directory do Azure usando o Azure PowerShell. |
| Propriedades: RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | A função que foi atribuída. Você pode determinar o nome de exibição da função usando o Azure PowerShell. |
| Propriedades: Scope | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	O recurso no qual foi feita a atribuição de função. Pode ser um recurso, um grupo de recursos ou uma assinatura. |

###Trechos de código de exemplo do PowerShell

Primeiro, este é um exemplo de código do Azure PowerShell para mapear um PrincipalId para um nome e tipo:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

Em seguida, estão alguns códigos de exemplo do Azure PowerShell para mapear um escopo para um nome e tipo de recurso:

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
Em seguida, estão alguns códigos de exemplo do Azure PowerShell para mapear um RoleDefinitionId para o nome de exibição da função:

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Script de exemplo do Azure PowerShell

Juntando tudo, este é um script de exemplo que recupera os eventos de atribuição de função para um determinado intervalo de datas e os retorna como uma tabela:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

E estes são os comandos para executar o script

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Problemas conhecidos ao usar o controle de acesso com base em função

Se você encontrar um problema ao usar o recurso do controle de acesso com base em função, consulte [Solucionar problemas do controle de acesso com base em função](role-based-access-control-troubleshooting.md) para qualquer problema conhecido que possa estar relacionado ao problema.


## Funções internas

O controle de acesso baseado em função do Azure é fornecido com as seguintes funções internas que podem ser atribuídas a usuários, grupos e serviços. Você não pode modificar a definição das funções integradas. Em uma versão futura do RBAC do Azure, você poderá definir funções personalizadas pela composição de um conjunto de ações de uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.

Clique no link correspondente para ver as **ações** e **não** as propriedades das ações de uma definição de função. A propriedade das **ações** especifica as ações permitidas em recursos do Azure. As cadeias de caracteres da ação podem usar caracteres curingas. A propriedade **não-ações** da definição de função especifica as ações que devem ser excluídas das ações permitidas.


Nome da função | Descrição
------------- | -------------  
[Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Permite gerenciar o serviço de gerenciamento de API, mas não acessá-lo.
[Colaborador de componente do Application Insights](#application-insights-component-contributor) | Permite gerenciar componentes do Application Insights, mas não acessá-los.
[Colaborador do BizTalk](#biztalk-contributor) | Permite gerenciar os serviços do BizTalk, mas não acessá-los.
[Colaborador do DB MySQL ClearDB](#cleardb-mysql-db-contributor) | Permite que você gerencie bancos de dados MySQL ClearDB, mas não acessá-los.
[Colaborador](#contributor) | Colaboradores podem gerenciar tudo, exceto o acesso.
[Colaborador da fábrica de dados](#data-factory-contributor) | Permite que você gerencie fábricas de dados, mas não acessá-las.
[Colaborador de conta do banco de dados de documento](#document-db-account-contributor) | Permite gerenciar contas do DocumentDB, mas não acessá-las.
[Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Permite gerenciar contas de sistemas inteligentes, mas não acessá-las.
[Colaborador de conta do APM NewRelic](#newrelic-apm-account-contributor) | Permite gerenciar novas contas e aplicativos de gerenciamento de desempenho de aplicativo Relic, mas não acessá-las.
[Proprietário](#owner) | O proprietário pode gerenciar tudo, incluindo o acesso.
[Leitor](#reader) | Os leitores podem ver tudo, mas não podem fazer alterações.
[Colaborador do Cache Redis](#redis-cache-contributor) | Permite gerenciar caches Redis, mas não acessá-los.
[Colaborador do banco de dados SQL](#sql-db-contributor) | Permite que você gerencie bancos de dados SQL, mas não acessá-los. Além disso, você não pode gerenciar suas políticas de segurança ou seus SQL servers principais.
[Gerenciador de Segurança do SQL](#sql-security-manager) | Permite que você gerencie políticas de segurança de SQL servers e bancos de dados, mas não acessá-las.
[Colaborador do SQL Server](#sql-server-contributor) | Permite que você gerencie SQL servers e bancos de dados, mas não acessá-los nem as políticas de segurança.
[Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Permite que você gerencie coleções de trabalho do Agendador, mas não acessá-las.
[Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite que você gerencie Serviços de pesquisa, mas não acessá-los.
[Colaborador da Conta de Armazenamento](#storage-account-contributor) | Permite que você gerencie contas de armazenamento, mas não acessá-las.
[Administrador de Acesso do Usuário](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure.
[Colaborador de Máquina Virtual](#virtual-machine-contributor) | Permite que você gerencie máquinas virtuais, mas não acessá-las e não a rede virtual ou conta de armazenamento que estão conectadas.
[Colaborador de Rede Virtual](#virtual-network-contributor) | Permite gerenciar redes virtuais, mas não acessá-las.
[Colaborador do Plano de Web](#web-plan-contributor) | Permite que você gerencie os planos da Web para sites, mas não acessá-los.
[Colaborador do Site](#website-contributor) | Permite que você gerencie sites (não os planos da Web), mas não acessá-los.


### Colaborador de serviço de gerenciamento de API

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Criar e gerenciar os serviços de gerenciamento de API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de componente do Application Insights

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Criar e gerenciar componentes do Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Criar e gerenciar testes da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Criar e gerenciar os serviços do BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do DB MySQL ClearDB

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>successbricks.cleardb/Databases/*</td>
<td>Criar e gerenciar bancos de dados MySQL ClearDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*</td>
<td>Criar e gerenciar recursos de todos os tipos</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Não é possível criar funções e atribuições de função </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Não é possível excluir funções e atribuições de função</td>
</tr>
</table>

### Colaborador da fábrica de dados

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Criar e gerenciar fábricas de dados</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de conta do banco de dados de documento

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Criar e gerenciar contas do DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de conta do sistemas inteligentes

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Criar e gerenciar contas do Intelligent Systems</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de conta do APM NewRelic

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Criar e gerenciar contas de gerenciamento de desempenho do aplicativo NewRelic</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Proprietário

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*</td>
<td>Criar e gerenciar recursos de todos os tipos</td>
</tr>
</table>

### Leitor

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*/leitura</td>
<td>Ler recursos de todos os tipos. No entanto, você não pode ler senhas.</td>
</tr>
</table>

### Colaborador do Cache Redis

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Criar e gerenciar Caches Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do banco de dados SQL

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leia os SQL Servers</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Criar e gerenciar bancos de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Recursos de assinatura de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Recursos do grupo de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Não é possível gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Não é possível gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Não é possível gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
</table>

### Gerenciador de Segurança do SQL

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leia os SQL Servers</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Criar e gerenciar políticas de auditoria do SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Ler bancos de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Criar e gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Criar e gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Criar e gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Criar e gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Criar e gerenciar bancos de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Recursos de assinatura de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Recursos do grupo de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
<tr>
<th colspan="2">Não-ações</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Não é possível gerenciar políticas de auditoria do banco de dados do SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Não é possível gerenciar políticas de conexão do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Não é possível gerenciar políticas de mascaramento de dados do banco de dados SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Não é possível gerenciar métricas de segurança do banco de dados SQL</td>
</tr>
</table>

### Colaborador de Coleções de Trabalho do Agendador

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Criar e gerenciar coleções de trabalhos do Agendador</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do Serviço de Pesquisa

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Criar e gerenciar serviços de pesquisa</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador da Conta de Armazenamento

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Criar e gerenciar contas de armazenamento</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Administrador de Acesso do Usuário

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>*/leitura</td>
<td>Ler recursos de todos os tipos</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Criar e gerenciar funções e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de Máquina Virtual

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Ler contas de armazenamento</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Recuperar chaves da conta de armazenamento</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Ler redes virtuais</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Ingressar em redes virtuais</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Ler endereços IP reservados</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Vincular para endereços IP reservados</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Criar e gerenciar serviços em nuvem</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Criar e gerenciar máquinas virtuais</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador de Rede Virtual

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Criar e gerenciar redes virtuais</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do Plano de Web

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Criar e gerenciar planos da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>

### Colaborador do Site

<table style=width:100%">
<tr>
<th colspan="2">Ações</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Ler planos da Web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Ingressar em planos da Web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Criar e gerenciar sites da Web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Criar e gerenciar certificados de site da Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Funções de leitura e atribuições de função</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Grupos de recursos de leitura</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Criar e gerenciar implantações do grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Criar e gerenciar regras de alerta</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Criar e gerenciar tíquetes de suporte</td>
</tr>
</table>


## Como fornecer comentários

Tente o Azure RBAC e nos envie [comentários](http://aka.ms/azurerbacfeedback).


## Próximas etapas

Veja alguns recursos adicionais para ajudar você a usar o controle de acesso com base em função:

+ [Gerenciar o controle de acesso com base em função com o Windows PowerShell](role-based-access-control-powershell.md)
+ [Gerenciar o controle de acesso com base em função com o Azure CLI](role-based-access-control-xplat-cli.md)
+ [Solucionar problemas do controle de acesso com base em função](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium e Basic](active-directory-editions.md)
+ [Como as assinaturas do Azure estão associadas ao AD do Azure](active-directory-how-subscriptions-associated-directory.md)
+ Para uma introdução para o gerenciamento de grupo de autoatendimento para os grupos de segurança, consulte o [Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=August15_HO8-->