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
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Por exemplo, você talvez queira implantar a máquina virtual de backup do Azure Site Recovery para um local e um grupo de recursos separados. O Resource Manager permite usar modelos aninhados com assinaturas e grupos de recursos de destino diferentes do que os usados para o modelo pai.

O grupo de recursos é o contêiner de ciclo de vida do aplicativo e de sua coleção de recursos. Crie o grupo de recursos fora do modelo e especifique o grupo de recursos de destino durante a implantação. Para obter uma introdução sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).

## <a name="specify-a-subscription-and-resource-group"></a>Especifique uma assinatura e um grupo de recursos

Para buscar um recurso diferente, você deve usar um modelo aninhado ou vinculado durante a implantação. O tipo de recurso `Microsoft.Resources/deployments` fornece parâmetros para `subscriptionId` e `resourceGroup`. Essas propriedades permitem que você especifique uma assinatura e um grupo de recursos diferentes para a implantação aninhada. Todos os grupos de recursos devem existir antes da execução da implantação. Se você não especificar a ID da assinatura nem o grupo de recursos, serão usados a assinatura e o grupo de recursos do modelo pai.

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

## <a name="deploy-the-template"></a>Implantar o modelo

Para implantar o modelo de exemplo, use a versão de maio de 2017 do Azure PowerShell ou da CLI do Azure ou uma posterior. Para esses exemplos, use o [modelo entre assinaturas](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) no GitHub.

### <a name="two-resource-groups-in-the-same-subscription"></a>Dois grupos de recursos na mesma assinatura

No PowerShell, para implantar duas contas de armazenamento em dois grupos de recursos na mesma assinatura, use:

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

Na CLI do Azure, para implantar duas contas de armazenamento em dois grupos de recursos na mesma assinatura, use:

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

Após a implantação ser concluída, você verá dois grupos de recursos. Cada grupo de recursos contém uma conta de armazenamento.

### <a name="two-resource-groups-in-different-subscriptions"></a>Dois grupos de recursos em diferentes assinaturas

No PowerShell, para implantar duas contas de armazenamento em duas assinaturas, use:

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

Na CLI do Azure, para implantar duas contas de armazenamento em duas assinaturas, use:

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

Para testar as diferentes maneiras em que a função `resourceGroup()` é resolvida, implante um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) que retorne o objeto de grupo de recursos para o modelo pai, para o modelo embutido e para o modelo vinculado. O modelo pai e o embutido são resolvidos para o mesmo grupo de recursos. O modelo vinculado é resolvido para o grupo de recursos vinculado.

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Para a CLI do Azure, use:

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
* Para obter mais informações sobre a implantação de um modelo que exija um token SAS, veja [Implantar modelo particular com o token SAS](resource-manager-powershell-sas-token.md).
