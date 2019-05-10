---
title: Automatizar a adição de um usuário de laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como automatizar a adição de um usuário de laboratório a um laboratório no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502040"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizar a adição de um usuário de laboratório a um laboratório no Azure DevTest Labs
O Azure DevTest Labs permite que você crie rapidamente ambientes de desenvolvimento e teste de autoatendimento por meio do portal do Azure. No entanto, se você tiver várias equipes e várias instâncias do DevTest Labs, automatizar o processo de criação pode economizar tempo. [Modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) permitem que você crie laboratórios, VMs do laboratório, imagens personalizadas, fórmulas e adicionar usuários de forma automática. Este artigo se concentra especificamente sobre como adicionar usuários a uma instância do DevTest Labs.

Para adicionar um usuário a um laboratório, você pode adicionar o usuário a **usuário do DevTest Labs** função para o laboratório. Este artigo mostra como automatizar a adição de um usuário a um laboratório usando uma das seguintes maneiras:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

## <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure
O modelo de Gerenciador de recursos de exemplo a seguir especifica um usuário a ser adicionado para o **usuário do DevTest Labs** função de um laboratório. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Se você estiver atribuindo a função no mesmo modelo que está criando o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de função e o laboratório. Para obter mais informações, consulte [definição de dependências nos modelos do Azure Resource Manager](/azure-resource-manager/resource-group-define-dependencies.md) artigo.

### <a name="role-assignment-resource-information"></a>Informações de recursos de atribuição de função
O recurso de atribuição de função precisa especificar o tipo e nome.

A primeira coisa a observar é que o tipo para o recurso não é `Microsoft.Authorization/roleAssignments` como seria um grupo de recursos.  Em vez disso, o tipo de recurso segue o padrão `{provider-namespace}/{resource-type}/providers/roleAssignments`. Nesse caso, o tipo de recurso será `Microsoft.DevTestLab/labs/providers/roleAssignments`.

O nome da atribuição de função em si precisa ser globalmente exclusivo.  O nome da atribuição usa o padrão de `{labName}/Microsoft.Authorization/{newGuid}`. O `newGuid` é um valor de parâmetro para o modelo. Isso garante que o nome da atribuição de função é exclusivo. Como não há nenhuma função de modelo para a criação de GUIDs, você precisa gerar um GUID usando qualquer ferramenta de gerador GUID.  

No modelo, o nome da atribuição de função é definido pelo `fullDevTestLabUserRoleName` variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades do recurso de atribuição de função
Uma atribuição de função define três propriedades. Ele precisa de `roleDefinitionId`, `principalId`, e `scope`.

### <a name="role-definition"></a>Definição de Função
A ID de definição de função é o identificador de cadeia de caracteres para a definição de função existente. A função ID está no formato `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

A ID é obtida por meio de assinatura `subscription().subscriptionId` função de modelo.  

Você precisa obter a definição de função para o `DevTest Labs User` função interna. Para obter o GUID para o [usuário do DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) função, você pode usar o [API de REST de atribuições de função](/rest/api/authorization/roleassignments) ou o [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

A ID de função é definida na seção de variáveis e nomeada `devTestLabUserRoleId`. No modelo, a ID de função é definida como: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID da Entidade
ID da entidade é a ID de objeto de usuário do Active Directory, grupo ou entidade de serviço que você deseja adicionar como um usuário de laboratório ao laboratório. O modelo usa o `ObjectId` como um parâmetro.

Você pode obter a ID de objeto, usando o [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) cmdlets do PowerShell. Esses cmdlets retornam um único ou listas de objetos do Active Directory que têm uma propriedade de ID, que é a ID de objeto que você precisa. O exemplo a seguir mostra como obter a ID de objeto de um usuário individual em uma empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Você também pode usar os cmdlets do Azure Active Directory PowerShell que incluem [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Escopo Especifica o recurso ou grupo de recursos para o qual a atribuição de função deve ser aplicada. Para obter recursos, o escopo está no formato: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. O modelo usa o `subscription().subscriptionId` função para preencher a `subscription-id` parte e o `resourceGroup().name` a função de modelo para preencher o `resource-group-name` parte. Usando essas funções significa que o laboratório para o qual você está atribuindo uma função deve existir na assinatura atual e o mesmo grupo de recursos no qual a implantação de modelo é feita. A última parte, `resource-name`, é o nome do laboratório. Esse valor é recebido por meio do parâmetro de modelo neste exemplo. 

O escopo da função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implantação do modelo
Primeiro, crie um arquivo de parâmetro (por exemplo: azuredeploy.parameters.json) que passa valores para parâmetros de modelo do Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Em seguida, use o [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) cmdlet do PowerShell para implantar o modelo do Resource Manager. O comando de exemplo a seguir atribui uma pessoa, grupo ou uma entidade de serviço à função de usuário do DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante observar que a implantação nome e a função de atribuição de grupo GUID precisa ser exclusivo. Se você tentar implantar uma atribuição de recurso com um GUID não exclusivo, você obterá um `RoleAssignmentUpdateNotPermitted` erro.

Se você planeja usar o modelo várias vezes para adicionar vários objetos do Active Directory à função de usuário do DevTest Labs para seu laboratório, considere o uso de objetos dinâmicos em seu comando do PowerShell. O exemplo a seguir usa o [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet para especificar o recurso implantação nome e a função de atribuição de grupo GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Conforme discutido na introdução, você cria uma nova atribuição de função do Azure para adicionar um usuário para o **usuário do DevTest Labs** função para o laboratório. No PowerShell, fazer isso usando o [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet. Esse cmdlet tem muitos parâmetros opcionais para permitir flexibilidade. O `ObjectId`, `SigninName`, ou `ServicePrincipalName` pode ser especificado como o objeto que está sendo concedido permissões.  

Aqui está um exemplo de comando de PowerShell do Azure que adiciona um usuário à função de usuário do DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Para especificar o recurso ao qual permissões estão sendo concedido pode ser especificado por uma combinação de `ResourceName`, `ResourceType`, `ResourceGroup` ou o `scope` parâmetro. Qualquer combinação de parâmetros é usada, fornecer informações suficientes para o cmdlet para identificar exclusivamente o objeto do Active Directory (usuário, grupo ou entidade de serviço), o escopo (grupo de recursos ou recurso) e a definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Use a Interface de linha de comando (CLI) do Azure
Na CLI do Azure, a adição de um usuário de laboratórios para um laboratório é feito usando o `az role assignment create` comando. Para obter mais informações sobre os cmdlets da CLI do Azure, consulte [gerenciar o acesso aos recursos do Azure usando RBAC e a CLI do Azure](../role-based-access-control/role-assignments-cli.md).

O objeto que está sendo concedido acesso pode ser especificado pela `objectId`, `signInName`, `spn` parâmetros. O laboratório no qual o objeto está sendo concedido acesso pode ser identificado pelo `scope` url ou uma combinação da `resource-name`, `resource-type`, e `resource-group` parâmetros.

O exemplo de CLI do Azure a seguir mostra como adicionar uma pessoa à função de usuário do DevTest Labs para o laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure](devtest-lab-vmcli.md)
- [Criar uma máquina virtual com o DevTest Labs usando o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Usar ferramentas de linha de comando para iniciar e parar máquinas de virtuais do Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

