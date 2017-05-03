---
title: Implantar recursos com a CLI do Azure e o modelo | Microsoft Docs
description: "Use o Azure Resource Manager e a CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure

Este tópico explica como usar a [CLI do Azure 2.0](/cli/azure/install-az-cli2) com modelos do Resource Manager para implantar seus recursos no Azure.  Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI.

Você pode obter o modelo (storage.json) usado nestes exemplos do artigo [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md#final-template). Para usar o modelo com esses exemplos, crie um arquivo JSON e adicione o conteúdo copiado.

## <a name="deploy-local-template"></a>Implantar o modelo local

Para começar a implantação rapidamente, use os comandos a seguir para implantar um modelo local com parâmetros embutidos:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```azurecli
"provisioningState": "Succeeded",
```

O exemplo anterior criou o grupo de recursos em sua assinatura padrão. Para usar uma assinatura diferente, adicione o comando [az account set](/cli/azure/account#set) após fazer logon.

## <a name="deploy-external-template"></a>Implantar modelo externo

Para implantar um modelo externo, use o parâmetro **template-uri**. O modelo pode estar em qualquer URI acessível publicamente (por exemplo, um arquivo na conta de armazenamento).
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Você pode proteger seu modelo exigindo um token SAS (Assinatura de Acesso Compartilhado) para acesso. Para obter mais informações sobre a implantação de um modelo que exija um token SAS, veja [Implantar modelo particular com o token SAS](resource-manager-cli-sas-token.md).

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

Para passar um arquivo de parâmetros local, use:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>Testar uma implantação

Para testar os valores de parâmetro e o modelo sem realmente implantar os recursos, use [az group deployment validate](/cli/azure/group/deployment#validate). Ele tem todas as mesmas opções de uso de arquivos locais ou remotos.

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>Depurar

Para obter informações sobre as operações de uma implantação com falha, use:
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).


## <a name="export-resource-manager-template"></a>Exportar modelo do Resource Manager
Para um grupo de recursos existente (implantado por meio da CLI do Azure ou com um dos outros métodos, por exemplo, o portal), você pode exibir o modelo do Resource Manager do grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.
2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Para exibir o modelo de um grupo de recursos, execute o comando [az group export](/cli/azure/group#export).

```azurecli
az group export --name ExampleGroup
```

Para saber mais, veja [Exportar um modelo do Azure Resource Manager desde os recursos existentes](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para usar o modo completo, use o parâmetro `mode`:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>Próximas etapas
* Para um script de exemplo completo que implanta um modelo, veja [Script de implantação do modelo do Resource Manager](resource-manager-samples-cli-deploy.md).
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter mais informações sobre a implantação de um modelo que exija um token SAS, veja [Implantar modelo particular com o token SAS](resource-manager-cli-sas-token.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

