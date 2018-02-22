---
title: "Implantar recursos do Azure para várias assinaturas e grupos de recursos | Microsoft Docs"
description: "Mostra como usar mais de um destino de assinatura e de grupo de recursos do Azure durante a implantação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: tomfitz
ms.openlocfilehash: 40b2d04fe829c51a58fb3bec1519a590a12cfdb8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos

Normalmente, você deve implantar todos os recursos em seu modelo em um único [grupo de recursos](resource-group-overview.md). No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Por exemplo, você talvez queira implantar a máquina virtual de backup do Azure Site Recovery para um local e um grupo de recursos separados. O Resource Manager permite usar modelos aninhados com assinaturas e grupos de recursos de destino diferentes do que os usados para o modelo pai.

> [!NOTE]
> Você pode implantar em apenas cinco grupos de recursos em uma única implantação.

## <a name="specify-a-subscription-and-resource-group"></a>Especifique uma assinatura e um grupo de recursos

Para buscar um recurso diferente, use um modelo aninhado ou vinculado. O tipo de recurso `Microsoft.Resources/deployments` fornece parâmetros para `subscriptionId` e `resourceGroup`. Essas propriedades permitem que você especifique uma assinatura e um grupo de recursos diferentes para a implantação aninhada. Todos os grupos de recursos devem existir antes da execução da implantação. Se você não especificar a ID da assinatura nem o grupo de recursos, serão usados a assinatura e o grupo de recursos do modelo pai.

Para especificar um grupo de recursos e assinatura diferentes, use:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Se os grupos de recursos estiverem na mesma assinatura, você poderá remover o valor **subscriptionId**.

O exemplo a seguir implanta duas contas de armazenamento: uma no grupo de recursos especificado durante a implantação e outra em um grupo de recursos especificado no parâmetro `secondResourceGroup`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Se você definir `resourceGroup` como o nome de um grupo de recursos que não existe, a implantação falhará.

Para implantar o modelo de exemplo, use o Azure PowerShell 4.0.0 ou posterior ou a CLI do Azure 2.0.0 ou posterior.

## <a name="use-the-resourcegroup-function"></a>Usar a função resourceGroup()

Para implantações entre grupos de recursos, a [função resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) é resolvida de forma diferente considerando como você especifica o modelo aninhado. 

Se você inserir um modelo dentro de outro modelo, a função resourceGroup() no modelo aninhado será resolvida para o grupo de recursos pai. Um modelo incorporado usa o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Se você vincular a um modelo separado, a função resourceGroup() no modelo vinculado será resolvida para o grupo de recursos aninhado. Um modelo incorporado usa o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>Modelos de exemplo

Os modelos a seguir demonstram várias implantações de grupo de recursos. Scripts para implantar os modelos são mostrados após a tabela.

|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[Modelo entre assinaturas](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Implanta uma conta de armazenamento para um grupo de recursos e uma conta de armazenamento a um segundo grupo de recursos. Inclua um valor para a ID de assinatura quando o segundo grupo de recursos está em uma assinatura diferente. |
|[Modelo de propriedades entre grupos de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Demonstra como a função `resourceGroup()` resolve. Ele não implanta nenhum recurso. |

### <a name="powershell"></a>PowerShell

No PowerShell, para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

No PowerShell, para implantar duas contas de armazenamento em **duas assinaturas**, use:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Para o PowerShell, para testar como o **objeto do grupo de recursos** resolve para o modelo pai, o modelo embutido e o modelo vinculado, use:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

### <a name="azure-cli"></a>CLI do Azure

Na CLI do Azure, para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Na CLI do Azure, para implantar duas contas de armazenamento em **duas assinaturas**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Para a CLI do Azure, para testar como o **objeto do grupo de recursos** resolve para o modelo pai, o modelo embutido e o modelo vinculado, use:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>Próximas etapas

* Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-powershell-sas-token.md).
