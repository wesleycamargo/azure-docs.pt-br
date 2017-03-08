---
title: Implantar recursos no Azure | Microsoft Docs
description: "Usar o Azure PowerShell ou a CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3
ms.lasthandoff: 02/21/2017


---
# <a name="deploy-resources-to-azure"></a>Implantar recursos no Azure

Este tópico mostra como implantar recursos na sua assinatura do Azure. Você pode usar o Azure PowerShell ou a CLI do Azure para implantar um modelo do Resource Manager que define a infraestrutura para sua solução.

Para uma introdução aos conceitos do Resource Manager, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Etapas para implantação

Este tópico pressupõe que você está implantando o [modelo de armazenamento de exemplo](#example-storage-template) deste tópico. Você pode usar um modelo diferente, mas os parâmetros que você passa são diferentes dos que são mostrados neste tópico.

Depois de criar um modelo, as etapas gerais para implantar o modelo são:

1. Faça logon na sua conta
2. Selecione a assinatura a ser usada (necessário apenas se você tiver várias assinaturas e quiser usar uma que não seja a assinatura padrão)
3. Criar um grupo de recursos
4. Implantar o modelo
5. Verificar o status da implantação

As seções a seguir mostram como executar essas etapas com o [PowerShell](#powershell) ou a [CLI do Azure](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Para instalar o Azure PowerShell, consulte [Introdução aos cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs).

2. Para começar a implantação de forma rápida, use os seguintes cmdlets:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  O cmdlet `Set-AzureRmContext` só é necessário se você deseja usar uma assinatura diferente da sua assinatura padrão. Para ver todas as suas assinaturas e suas IDs, use:

  ```powershell
  Get-AzureRmSubscription
  ```

3. A implantação pode levar alguns minutos para ser concluída. Ao terminar, você verá uma mensagem semelhante à:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Para ver que a conta de armazenamento e o grupo de recursos foram implantados na sua assinatura, use:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Você pode especificar os parâmetros de modelo como parâmetros do PowerShell ao implantar um modelo. O exemplo anterior não incluía nenhum parâmetro de modelo, de maneira que os valores padrão do modelo foram usados. Para implantar outra conta de armazenamento e fornecer valores de parâmetro para o prefixo de nome de armazenamento e para o SKU da conta de armazenamento, use:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Agora você tem duas contas de armazenamento no seu grupo de recursos. 

## <a name="azure-cli"></a>CLI do Azure

1. Para instalar a CLI do Azure, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-az-cli2).

2. Para começar a implantação de forma rápida, use os seguintes comandos:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  O comando `az account set` só é necessário se você deseja usar uma assinatura diferente da sua assinatura padrão. Para ver todas as suas assinaturas e suas IDs, use:

  ```azurecli
  az account list
  ```

3. A implantação pode levar alguns minutos para ser concluída. Ao terminar, você verá uma mensagem semelhante à:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Para ver que a conta de armazenamento e o grupo de recursos foram implantados na sua assinatura, use:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Você pode especificar os parâmetros de modelo como parâmetros do PowerShell ao implantar um modelo. O exemplo anterior não incluía nenhum parâmetro de modelo, de maneira que os valores padrão do modelo foram usados. Para implantar outra conta de armazenamento e fornecer valores de parâmetro para o prefixo de nome de armazenamento e para o SKU da conta de armazenamento, use:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Agora você tem duas contas de armazenamento no seu grupo de recursos. 

## <a name="example-storage-template"></a>Modelo de armazenamento de exemplo

Use o modelo de exemplo a seguir para implantar uma conta de armazenamento na sua assinatura:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações detalhadas sobre como usar o PowerShell para implantar modelos, consulte [Implantar recursos com modelos do Resource Manager e com o Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Para obter informações detalhadas sobre como usar a CLI do Azure para implantar modelos, consulte [Implantar recursos com modelos do Resource Manager e com a CLI do Azure](/azure/azure-resource-manager/resource-group-template-deploy-cli).




