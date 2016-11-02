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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>


# <a name="role-assignments-template-schema"></a>Atribuições de função - esquema do modelo

Atribui um usuário, grupo ou entidade de serviço a uma função em um escopo especificado.

## <a name="resource-format"></a>Formato de recurso

Para criar uma atribuição de função, adicione o esquema a seguir à seção de recursos do seu modelo.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| type | Sim    | O tipo de recurso a ser criado.<br /><br /> Para grupo de recursos:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Para recurso:<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| apiVersion |Sim | A versão da API a ser usada para criar o recurso.<br /><br /> Use **2015-07-01**. | 
| name | Sim | Um identificador global exclusivo para a nova atribuição de função. |
| dependsOn | Não | Uma matriz de nomes de recursos ou identificadores exclusivos de recursos separados por vírgulas.<br /><br />A coleção de recursos da qual esta atribuição de função depende. Se atribuir uma função com escopo a um recurso e esse recurso for implantado no mesmo modelo, inclua esse nome de recurso neste elemento para garantir que o recurso seja implantado primeiro. |
| propriedades | Sim | O objeto de propriedades que identifica a definição da função, a entidade e o escopo. |

### <a name="properties-object"></a>properties object

| Nome | Obrigatório | Descrição |
| ---- | -------- | ----------- |
| roleDefinitionId | Sim |  O identificador de uma definição de função existente a ser usado na atribuição de função.<br /><br /> Use o seguinte formato:<br /> **/subscriptions/{id-assinatura}/providers/Microsoft.Authorization/roleDefinitions/{id-definição-função}** |
| principalId | Sim | O identificador global exclusivo de uma entidade existente. Esse valor mapeia para a ID dentro do diretório e pode apontar para um usuário, uma entidade de serviço ou um grupo de segurança. |
| scope | Não | O escopo ao qual essa atribuição de função se aplica.<br /><br />Para grupos de recursos , use:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />Para recursos, use:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Como usar o recurso de atribuição de função

Você pode adicionar uma atribuição de função a seu modelo quando você precisa adicionar um usuário, grupo ou entidade de serviço a uma função durante a implantação. As atribuições de função são herdadas de níveis mais altos de escopo, então se você já tiver adicionado uma entidade a uma função no nível de assinatura você não precisa reatribuí-la ao recurso ou grupo de recursos.

Há muitos valores de identificador que você precisa fornecer ao trabalhar com atribuições de função. Você pode recuperar os valores pela CLI do PowerShell ou do Azure.

### <a name="powershell"></a>PowerShell

O nome da atribuição de função requer um identificador global exclusivo. Você pode gerar um novo identificador para **nome** com:

    $name = [System.Guid]::NewGuid().toString()

Você pode recuperar o identificador para definição de função com:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Você pode recuperar o identificador da entidade com um dos comandos a seguir.

Para um grupo chamado **Auditores**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para um usuário chamado **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para uma entidade de serviço chamada **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>CLI do Azure

Você pode recuperar o identificador para definição de função com:

    azure role show Reader --json | jq .[].Id -r

Você pode recuperar o identificador da entidade com um dos comandos a seguir.

Para um grupo chamado **Auditores**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Para um usuário chamado **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Para uma entidade de serviço chamada **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemplos

O modelo a seguir recebe um identificador para uma função e um identificador para um usuário, grupo ou entidade de serviço. Ele atribui a função no nível do grupo de recursos.

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



O próximo modelo cria uma conta de armazenamento e atribui a função de leitor à conta de armazenamento. Os identificadores para dois grupos e a função do leitor foram incluídos no modelo para simplificar a implantação. Esses valores podem ser recuperados no momento da implantação por meio de script e transmitidos como parâmetros.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modelos de início rápido

Os modelos a seguir mostram como usar o recurso de atribuição de função:

- [Atribuir função interna ao grupo de recursos](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Atribuir função interna a VM existente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Atribuir função interna a VMs existentes múltiplas](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure Active Directory](./active-directory/role-based-access-control-configure.md).



<!--HONumber=Oct16_HO2-->


