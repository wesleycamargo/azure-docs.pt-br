---
title: Implantar recursos com a CLI do Azure e o modelo | Microsoft Docs
description: Use o Azure Resource Manager e a CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2018
ms.author: tomfitz
ms.openlocfilehash: 8c3d208b12166a590c68753fb4f58c9bb6e55610
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225524"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure

Este artigo explica como usar a CLI do Azure com modelos do Resource Manager para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).  

O modelo do Resource Manager que você implanta pode ser um arquivo local do seu computador ou um arquivo externo que está localizado em um repositório como o GitHub. O modelo que você implanta neste artigo está disponível na seção [Modelo de exemplo](#sample-template) ou como um [modelo de conta de armazenamento no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Se você não tiver a CLI do Azure instalada, você pode usar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Implantar o modelo local

Ao implantar recursos no Azure, você:

1. Entre na sua conta do Azure
2. Crie um grupo de recursos que atue como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.
3. Implanta no grupo de recursos o modelo que define os recursos a serem criados

Um modelo pode incluir parâmetros que permitem personalizar a implantação. Por exemplo, você pode fornecer valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para o SKU da conta de armazenamento. 

O exemplo a seguir cria um grupo de recursos e implanta um modelo do computador local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Implantar modelo externo

Em vez de armazenar modelos do Resource Manager no computador local, talvez você prefira armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **template-uri**. Use o URI do exemplo para implantar o modelo de exemplo do GitHub.
   
```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

No Cloud Shell, use os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Implantar em mais de um grupo de recursos ou assinatura

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Você pode implantar em apenas cinco grupos de recursos em uma única implantação. Para saber mais, consulte [Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](resource-manager-cross-resource-group-deployment.md).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Para implantações que falharam, você pode especificar que uma implantação anterior do seu histórico de implantação seja reimplantada automaticamente. Para usar essa opção, as implantações devem ter nomes exclusivos para que possam ser identificadas no histórico. Se você não tiver nomes exclusivos, a implantação atual com falha pode substituir a implantação bem-sucedida anteriormente no histórico. Você só pode usar essa opção com implantações de nível raiz. Implantações de um modelo aninhado não estão disponíveis para reimplantação.

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `--rollback-on-error` como um sinalizador.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para reimplantar uma implantação específica, use o `--rollback-on-error` parâmetro e forneça o nome da implantação.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

A implantação especificada deve ter êxito.

## <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve estar no seguinte formato:

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

Observe que a seção de parâmetros inclui um nome de parâmetro que corresponde ao parâmetro definido no seu modelo (storageAccountType). O arquivo de parâmetros contém um valor para o parâmetro. Esse valor é passado automaticamente ao modelo durante a implantação. Você pode criar vários arquivos de parâmetros para diferentes cenários de implantação e, em seguida, passar o arquivo de parâmetros apropriado. 

Copie o exemplo anterior e salve-o como um arquivo chamado `storage.parameters.json`.

Para passar um arquivo de parâmetros local, use `@` para especificar um arquivo local chamado storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testar uma implantação de modelo

Para testar os valores de parâmetro e o modelo sem realmente implantar os recursos, use [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se nenhum erro for detectado, o comando retornará informações sobre a implantação de teste. Especificamente, observe que o valor de **erro** é null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, tentar passar um valor incorreto para o SKU da conta de armazenamento, retornará o seguinte erro:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se seu modelo tem um erro de sintaxe, o comando retorna um erro indicando que ele não foi possível analisar o modelo. A mensagem indica o número da linha e a posição do erro de análise.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="sample-template"></a>Modelo de exemplo

O modelo a seguir é utilizado para os exemplos deste artigo. Copie-o e salve-o como um arquivo chamado storage.json. Para entender como criar esse modelo, confira [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* Os exemplos deste artigo implantam recursos em um grupo de recursos na sua assinatura padrão. Para usar outra assinatura, confira [Manage multiple Azure subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli) (Gerenciar várias assinaturas do Azure).
* Para especificar como lidar com recursos existentes no grupo de recursos, mas que não estão definidos no modelo, consulte [Modos de implementação do Azure Resource Manager](deployment-modes.md).
* Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-cli-sas-token.md).
* Para distribuir seu serviço com segurança em várias regiões, confira [Gerenciador de Implantação do Azure](deployment-manager-overview.md).
