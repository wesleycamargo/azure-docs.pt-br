---
title: Implantar recursos com o PowerShell e o modelo | Microsoft Docs
description: Use o Azure Resource Manager e o Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: a816542860a96a8b0dbbeaa63202b6cba4d24acc
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294971"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implantar recursos com modelos do Resource Manager e o Azure PowerShell

Este artigo explica como usar o Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).

O modelo do Resource Manager que você implementa pode ser um arquivo local em sua máquina ou um arquivo externo localizado em um repositório como o GitHub. O modelo implementado neste artigo está disponível como [modelo de conta de armazenamento no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

Se necessário, instale o módulo do Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Implantar um modelo do computador local

Ao implantar recursos no Azure, você:

1. Entre na sua conta do Azure
2. Crie um grupo de recursos que atue como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.
3. Implanta no grupo de recursos o modelo que define os recursos a serem criados

Um modelo pode incluir parâmetros que permitem personalizar a implantação. Por exemplo, você pode fornecer valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para o SKU da conta de armazenamento.

O exemplo a seguir cria um grupo de recursos e implanta um modelo do computador local:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Implantar um modelo de uma fonte externa

Em vez de armazenar modelos do Resource Manager no computador local, talvez você prefira armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **TemplateUri**. Use o URI do exemplo para implantar o modelo de exemplo do GitHub.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

No Cloud Shell, use os seguintes comandos:

```powershell
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Implantar em mais de um grupo de recursos ou assinatura

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Você pode implantar em apenas cinco grupos de recursos em uma única implantação. Para saber mais, consulte [Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameters"></a>parâmetros

Para passar valores de parâmetros, você pode usar parâmetros inline ou um arquivo de parâmetros. Os exemplos anteriores neste artigo mostram parâmetros inline.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros inline, forneça os nomes do parâmetro com o comando `New-AzResourceGroupDeployment`. Por exemplo, para passar uma string e array para um template, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro pode ser um arquivo local ou um arquivo externo com um URI acessível.

O arquivo de parâmetro deve estar no seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observe que a seção de parâmetros inclui um nome de parâmetro que corresponde ao parâmetro definido no seu modelo (storageAccountType). O arquivo de parâmetros contém um valor para o parâmetro. Esse valor é passado automaticamente ao modelo durante a implantação. Você pode criar mais de um arquivo de parâmetro e, em seguida, passar o arquivo de parâmetro apropriado para o cenário. 

Copie o exemplo anterior e salve-o como um arquivo chamado `storage.parameters.json`.

Para passar um arquivo de parâmetro local, use o **TemplateParameterFile**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um arquivo de parâmetro externo, use o **TemplateParameterUri**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

No entanto, quando você usa um arquivo de parâmetro externo, não é possível transmitir outros valores em linha ou em um arquivo local. Quando você especificar um arquivo de parâmetro no parâmetro **TemplateParameterUri**, todos os parâmetros embutidos serão ignorados. Forneça todos os valores de parâmetro no arquivo externo. Se o seu modelo incluir um valor confidencial que você não pode incluir no arquivo de parâmetros, adicione esse valor a um cofre de chaves ou forneça dinamicamente todos os valores de parâmetros inline.

### <a name="parameter-name-conflicts"></a>Conflitos de nome de parâmetro

Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, o PowerShell apresentará o parâmetro do modelo com o postfix **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Você é solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

## <a name="test-a-template-deployment"></a>Testar uma implantação de modelo

Para testar seus valores de parâmetro e modelo sem realmente implantar os recursos, use [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Se nenhum erro for detectado, o comando será concluído sem uma resposta. Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, passando um valor incorreto para a SKU da conta de armazenamento, retorna o seguinte erro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se seu modelo tem um erro de sintaxe, o comando retorna um erro indicando que ele não foi possível analisar o modelo. A mensagem indica o número da linha e a posição do erro de análise.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Próximas etapas
* Os exemplos deste artigo implantam recursos em um grupo de recursos na sua assinatura padrão. Para usar outra assinatura, confira [Manage multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Gerenciar várias assinaturas do Azure).
* Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
* Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-powershell-sas-token.md).
* Para distribuir com segurança seu serviço para mais de uma região, consulte [Azure Deployment Manager](deployment-manager-overview.md).
