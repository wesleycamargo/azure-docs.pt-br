---
title: "Criar Experimentação do Azure Machine Learning com o modelo do Azure Resource Manager | Microsoft Docs"
description: "Este artigo fornece um exemplo para criar uma conta da Experimentação do Azure Machine Learning usando um modelo do Azure Resource Manager."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 2efffd14c748a070fff59bcf9dd21c02eb9f6dcd
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Configurar o serviço de Experimentação do Azure Machine Learning

## <a name="overview"></a>Visão geral
A conta, o espaço de trabalho e o projeto do Serviço de Experimentação do Azure Machine Learning são recursos do Azure. Dessa forma, eles podem ser implantados usando modelos do Resource Manager. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Implantar um modelo
A implantação de um modelo requer apenas algumas etapas na Interface de Linha de Comando do Azure ou no portal do Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Implantar um modelo da linha de comando
Usando a interface de linha de comando, um único comando pode implantar um modelo em um grupo de recursos existente.
Veja a seguir para obter informações sobre como criar um modelo.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Implantar um modelo do portal do Azure
Se preferir, você também pode usar o portal do Azure para criar e implantar um modelo. Proceda da seguinte maneira:

1. Navegue até o [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e procure "modelos".
3. Selecione **Modelos**.
4. Clique em **+ Adicionar** e copie as informações do modelo. 
5. Selecione o modelo criado na etapa 4 e clique em **Implantar**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Criar um modelo de um recurso do Azure existente no portal do Azure
Se você já tiver uma conta da Experimentação do Azure Machine Learning disponível, no [portal do Azure](https://portal.azure.com), poderá gerar um modelo desse recurso. 

1. Navegue até uma Conta de Experimentação do Azure no [portal do Azure](https://portal.azure.com).
2. Em **configurações**, clique em **Script de automação**.
3. Baixe o modelo. 

Como alternativa, você pode editar manualmente os arquivos de modelo. Veja a seguir um exemplo de um modelo e de arquivos de parâmetros. 

### <a name="template-file-example"></a>Exemplo de arquivo de modelo
Crie um arquivo chamado "arquivo-modelo.json" com o conteúdo abaixo. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>parâmetros 
Crie um arquivo com o conteúdo abaixo e salve-o como <parametros.json>. 

Há três valores que podem ser alterados. 
* AccountName: o nome da conta de experimentação.
* Local: uma das regiões do Azure com suporte.
* SKU da Conta de Armazenamento: o Azure ML dá suporte somente ao armazenamento padrão, e não ao premium. Para saber mais sobre o armazenamento, veja [introdução ao armazenamento](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* [Criar e instalar o Azure Machine Learning](quickstart-installation.md)
