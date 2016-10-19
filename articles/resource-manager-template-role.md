<properties
   pageTitle="Modelo do Gerenciador de Recursos para atribuições de funções | Microsoft Azure"
   description="Mostra o esquema do Gerenciador de Recursos para a implantação de uma atribuição de função por meio de um modelo."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Atribuições de função - esquema do modelo

Atribui um usuário, grupo ou entidade de serviço a uma função em um escopo especificado.

## Formato de esquema

Para criar uma atribuição de função, adicione o esquema a seguir à seção de recursos do seu modelo.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2014-10-01-preview",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Valor | Descrição |
| ---- | ---- | ---- |
| type | Enum<br />Obrigatório<br />**Microsoft.Authorization/roleAssignments** | O tipo de recurso a ser criado. |
| apiVersion | Enum<br />Obrigatório<br />**2014-10-01-preview** | A versão da API a ser usada para criar o recurso. |  
| name | String<br />Obrigatório<br />**Identificador global exclusivo** | Um identificador para a nova atribuição de função. |
| dependsOn | Array<br />Opcional<br />Uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. | A coleção de recursos da qual esta atribuição de função depende. Se atribuir uma função com escopo a um recurso e esse recurso for implantado no mesmo modelo, inclua esse nome de recurso neste elemento para garantir que o recurso seja implantado primeiro. | 
| propriedades | Object<br />Obrigatório<br />[objeto de propriedades](#properties) | Um objeto que identifica o escopo, a entidade e a definição de função. |  

<a id="properties" />

### Objeto de propriedades

| Nome | Value1 | Descrição |
| ---- | ---- | ---- |
| roleDefinitionId | String <br />Obrigatório <br /><br /> **/subscriptions/{id-assinatura}/providers/Microsoft.Authorization/roleDefinitions/{id-definição-função}** | O identificador de uma definição de função existente a ser usado na atribuição de função. |
| principalId | String<br />Obrigatório <br /><br /> **Identificador global exclusivo** | O identificador de uma entidade existente. Isso mapeia para a ID dentro do diretório e pode apontar para um usuário, uma entidade de serviço ou um grupo de segurança. |
| scope | String<br />Obrigatório <br /><br /> **/subscriptions/{id-assinatura}/resourceGroups/{nome-grupo-recursos}** (para grupos de recursos)<br /> ou<br />**/subscriptions/{id-assinatura}/resourceGroups/{nome-grupo-recursos}/providers/{provider-namespace}/{tipo-recurso}/{nome-recurso}** (para recursos) | O escopo ao qual essa atribuição de função se aplica. |


## Como usar o recurso de atribuição de função

Você pode adicionar uma atribuição de função a seu modelo quando você precisa adicionar um usuário, grupo ou entidade de serviço a uma função durante a implantação. As atribuições de função são herdadas de níveis mais altos de escopo, então se você já tiver adicionado uma entidade a uma função no nível de assinatura você não precisa reatribuí-la ao recurso ou grupo de recursos.

Você pode gerar um novo identificador para **nome** com:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Você pode recuperar o identificador global exclusivo para a definição de função com:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Você pode recuperar o identificador da entidade com um dos comandos a seguir.

Para um grupo chamado **Auditores**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para um usuário chamado **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para uma entidade de serviço chamada **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Exemplos

O exemplo a seguir atribui um grupo a uma função para o grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }

## Modelos de início rápido

Os modelos a seguir mostram como usar o recurso de atribuição de função:

- [Atribuir função interna ao grupo de recursos](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Atribuir função interna a VM existente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Atribuir função interna a VMs existentes múltiplas](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## Próximas etapas

- Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0928_2016-->