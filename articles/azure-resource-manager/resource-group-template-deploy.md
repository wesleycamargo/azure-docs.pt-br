---
title: Implantar recursos com o PowerShell e o modelo | Microsoft Docs
description: "Use o Azure Resource Manager e o Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implantar recursos com modelos do Resource Manager e o Azure PowerShell

Este tópico explica como usar o Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure. Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI.

Você pode obter o modelo (storage.json) usado nestes exemplos do artigo [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md#final-template). Para usar o modelo com esses exemplos, crie um arquivo JSON e adicione o conteúdo copiado.

## <a name="deploy-local-template"></a>Implantar o modelo local
Para começar a implantação rapidamente, use os cmdlets a seguir para implantar um modelo local com parâmetros embutidos. 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```powershell
ProvisioningState       : Succeeded
```

O exemplo anterior criou o grupo de recursos em sua assinatura padrão. Para usar uma assinatura diferente, adicione o cmdlet [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) após fazer logon.

## <a name="deploy-external-template"></a>Implantar o modelo externo

Para implantar um modelo externo, use o parâmetro **TemplateUri**. O modelo pode estar em qualquer URI acessível publicamente (como um arquivo na conta de armazenamento).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

Você pode proteger seu modelo exigindo um token de assinatura de acesso compartilhado (SAS) para acesso. Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-powershell-sas-token.md).

## <a name="parameter-files"></a>Arquivos de parâmetros

Os exemplos anteriores mostraram como passar parâmetros como valores embutidos. Você pode especificar valores de parâmetro em um arquivo e passar esse arquivo durante a implantação. 

O arquivo de parâmetro deve estar no seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Para passar um arquivo de parâmetro local, use o **TemplateParameterFile**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um arquivo de parâmetro externo, use o **TemplateParameterUri**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

No entanto, quando você usa um arquivo de parâmetro externo, não é possível passar outros valores embutidos ou de um arquivo local. Quando você especificar um arquivo de parâmetro no parâmetro **TemplateParameterUri**, todos os parâmetros embutidos serão ignorados. Forneça todos os valores de parâmetro no arquivo externo. Se o seu modelo incluir um valor confidencial que não podem ser incluído no arquivo de parâmetros, adicione esse valor em um cofre de chaves ou forneça dinamicamente todos os valores de parâmetro embutidos.

Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, o PowerShell apresentará o parâmetro do modelo com o postfix **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

## <a name="test-a-deployment"></a>Testar uma implantação

Para testar seus valores de parâmetro e modelo sem realmente implantar os recursos, use [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). Ele tem as mesmas opções de uso de arquivos locais ou remotos.

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>Conteúdo do log de implantação para depuração

As informações sobre operações de implantação são automaticamente registradas nos logs de atividade. No entanto, para registrar informações adicionais sobre a implantação que podem ajudar a solucionar quaisquer erros de implantação, use o parâmetro **DeploymentDebugLogLevel**. Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Para saber mais sobre como exibir os logs, veja [Exibir logs de atividades para auditar ações em recursos](resource-group-audit.md).

## <a name="export-resource-manager-template"></a>Exportar modelo do Resource Manager
Para um grupo de recursos existente (implantado por meio do PowerShell ou com um dos outros métodos, por exemplo, o portal), você pode exibir o modelo do Resource Manager do grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.
2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Para ver o modelo de um grupo de recursos, execute o cmdlet [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) .

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

Para saber mais, veja [Exportar um modelo do Azure Resource Manager desde os recursos existentes](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para usar o modo completo, use o parâmetro `Mode`:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>Próximas etapas
* Para um script de exemplo completo que implanta um modelo, veja [Script de implantação do modelo do Resource Manager](resource-manager-samples-powershell-deploy.md).
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-powershell-sas-token.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).


