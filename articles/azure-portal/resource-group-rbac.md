<properties
   pageTitle="Gerenciar e auditar o acesso a recursos"
   description="Use o controle de acesso baseado em função (RBAC) para gerenciar permissões de usuário para recursos implantados no Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Gerenciar e auditar o acesso a recursos

Com o Gerenciador de Recursos do Azure, você pode garantir que os usuários em sua organização tenham as permissões apropriadas para gerenciar ou acessar recursos. O Gerenciador de Recursos se beneficia de controle de acesso baseado em função (RBAC) para que você pode facilmente aplicar diretivas de segurança para recursos individuais ou grupos de recursos. Por exemplo, você pode conceder um acesso de usuário a uma máquina virtual específica em uma assinatura ou oferecer ao usuário a capacidade de gerenciar todos os sites em uma assinatura, mas nenhum outro recurso.

## Conceitos

Há alguns conceitos importantes sobre o controle de acesso baseado em função para que você possa compreender:

1. Principal - entidade que recebeu permissão, como um usuário, o grupo de segurança ou o aplicativo.
2. Função - conjunto de ações permitidas
3. Escopo - o nível ao qual uma função se aplica, como a assinatura, um grupo de recursos ou um recurso.
3. Atribuição de função – o processo de adição de uma entidade a uma função e definição do escopo.

## Exemplos de função
Para entender os conceitos RBAC, vejamos alguns exemplos de definições de função comuns:

| Função | Ações permitidas |
| ------- | ----------------- |
| Leitor | */Read (leitura nada) | | Proprietário | * (Leitura/gravação nada) |

Para atribuir a função do **Leitor** função ao **Usuário A** para o grupo de recursos denominado **ExampleGroup**, e a função **Proprietário** ao **Usuário B** para a assinatura inteira, atribua o seguinte:

| Função | Principal | Escopo |
| --------|-------------|---------- |
| Leitor | Usuário A | /Subscriptions/ {subscriptionId} / resourceGroups/examplegroup |
| Proprietário | Usuário B | /Subscriptions/ {subscriptionId} |

## Cenários de exemplo

Neste tópico, você verá como executar os seguintes cenários comuns por meio do PowerShell do Azure, CLI do Azure para Mac, Linux e Windows e API REST.

1. Exiba funções disponíveis em uma assinatura e as ações permitidas para essas funções.
2. Conceda permissões de leitura para os membros de um grupo na assinatura.
3. Conceda permissões de Colaborador para um aplicativo a fim de permitir que o aplicativo gerencie recursos dentro do grupo de recursos.
4. Conceda permissões de proprietário em um site específico a um único usuário.
5. Lista dos logs de auditoria do grupo de recursos.


## Como usar o PowerShell para gerenciar o acesso
Se você ainda não tiver a versão mais recente do Azure PowerShell instalado, consulte [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Abra o console do PowerShell do Azure.

1. Faça logon em sua conta do Azure com suas credenciais. O comando retorna as informações sobre sua conta.

        PS C:> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

        PS C:> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Alterne para o módulo Gerenciador de Recursos do Azure.

        PS C:> Switch-AzureMode AzureResourceManager

### Exibir funções disponíveis
Para exibir as funções disponíveis para sua assinatura, execute o comando **Get-AzureRoleDefinition**.

    PS C:> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### Conceda permissão de leitor a um grupo para a assinatura.
1. Analise a definição de função do **Leitor**, fornecendo o nome da função ao executar o comando **Get-AzureRoleDefinition**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        PS C:> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. Obter grupo de segurança necessário, executando o comando **Get-AzureADGroup**. Forneça o nome real do grupo em sua assinatura. ExampleAuditorGroup é mostrado abaixo.

        PS C:> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. Crie a atribuição de função para o grupo de segurança de auditor. Quando o comando for concluído, a nova atribuição de função é retornada.

        PS C:> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###Conceda permissões de Colaborador para um aplicativo para um grupo de recursos.
1. Examine a definição de função do **Colaborador**, fornecendo o nome da função ao executar o comando **Get-AzureRoleDefinition**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        PS C:> Get-AzureRoleDefinition Contributor

2. Obtenha o Id do objeto principal de serviço executando o comando **Get-AzureADServicePrincipal** e forneça o nome do aplicativo em sua assinatura. ExampleApplication é mostrado abaixo.

        PS C:> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. Crie as atribuições de função para a entidade de serviço executando o comando **New-AzureRoleAssignment**.

        PS C:> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Para obter uma explicação mais completa da configuração de um aplicativo do Active Directory do Azure e uma entidade de serviço, consulte [Autenticar uma entidade de serviço com o Gerenciador de recursos do Azure](../resource-group-authenticate-service-principal.md).

###Conceda permissões de proprietário para um usuário de um recurso.
1. Analise a definição de função do **Proprietário**, fornecendo o nome da função ao executar o comando **Get-AzureRoleDefinition**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        PS C:> Get-AzureRoleDefinition Owner

2. Crie as atribuições de função do usuário.

        PS C:> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Lista dos logs de auditoria do grupo de recursos.
Para obter o log de auditoria de um grupo de recursos, execute o comando **Get-AzureResourceGroupLog**.

      PS C:> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## Como usar a CLI do Azure para Mac, Linux e Windows

Se você não tiver a CLI do Azure para Mac, Linux e Windows instalada ou não tiver configurado sua conta organizacional para usar com a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

1. Faça logon em sua conta do Azure com suas credenciais. O comando retorna o resultado de seu logon.

        azure login

        ...
        info:    login command OK

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

        azure account set <YourSubscriptionNameOrId>

3. Alternar para o módulo do Gerenciador de Recursos do Azure. Você receberá a confirmação do novo modo.

        azure config mode arm
        
        info:     New mode is arm

### Exibir funções disponíveis
Exiba todas as funções disponíveis para sua assinatura. Retorna uma lista de definições de função.

    azure role list

### Conceda permissão de leitor a um grupo para a assinatura.
1. Obtenha a definição de função para a função do **Leitor**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Obtenha o grupo de segurança necessários e seu objectId pesquisando o grupo com base no nome. ExampleAuditorGroup é mostrado no exemplo a seguir.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Crie a atribuição de função para o grupo de segurança.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Conceda permissões de Colaborador para um aplicativo para um grupo de recursos.
1. Obtenha a definição de função para a função de **Colaborador**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        azure role show Contributor

2. Obtenha o ID de objeto para o aplicativo. Forneça o nome do aplicativo a ser recuperado.

        azure ad sp show --search ExampleApplicationName

2. Crie a atribuição de função para o aplicativo.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Conceda permissões de proprietário para um usuário de um site específico.
1. Obtenha a definição de função para a função do **Proprietário**. Certifique-se de que as ações permitidas são as que você pretende atribuir.

        azure role show Owner

2. Crie a atribuição de função para o usuário.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Lista dos logs de auditoria do grupo de recursos.
Para obter o log de auditoria para um grupo de recursos, execute o comando **exibir log do grupo azure** e forneça o nome do grupo de recursos desejado.

         azure group log show ExampleGroupName


## Como usar a API REST
Para gerenciar o controle de acesso baseado em função por meio da API REST do Gerenciador de recursos do Azure, você deve definir parâmetros e cabeçalhos comuns (incluindo tokens de autenticação) ao enviar as solicitações. Para obter informações, consulte [parâmetros e cabeçalhos comuns](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Para descobrir as versões de api com suporte, execute:

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

Você pode usar a versão `2014-10-01-preview` deste tópico.

###Criar uma atribuição de função
Obtenha as definições de função disponíveis

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Crie a atribuição de função.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Excluir uma atribuição de função

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Próximas etapas

- [Controle de acesso baseado em função no portal do Microsoft Azure](../role-based-access-control-configure.md)
- [Criar uma nova entidade de serviço do Azure usando o portal clássico do Azure](../resource-group-create-service-principal-portal.md)
- [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=July15_HO3-->