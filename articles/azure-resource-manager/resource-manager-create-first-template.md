---
title: Criar o primeiro modelo do Azure Resource Manager | Microsoft Docs
description: "Um guia passo a passo para criar seu primeiro modelo do Azure Resource Manager. Ele mostra como usar a referência de modelo para uma conta de armazenamento para criar o modelo."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Criar seu primeiro modelo do Azure Resource Manager
Este tópico explica as etapas de criação de seu primeiro modelo do Azure Resource Manager. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md). Se você já tiver recursos e quiser obter um modelo para esses recursos, consulte [Exportar um modelo do Azure Resource Manager de recursos existentes](resource-manager-export-template.md).

Para criar e revisar os modelos, você precisa de um editor de JSON. [Visual Studio Code](https://code.visualstudio.com/) é um editor de código entre plataformas aberto e leve. Ele dá suporte à criação e edição de modelos do Resource Manager por meio de uma extensão. Este tópico pressupõe que você esteja usando o VS Code; no entanto, se você tiver outro editor de JSON (como o Visual Studio), use-o.

## <a name="get-vs-code-and-extension"></a>Obter o VS Code e a extensão
1. Se for necessário, instale o VS Code pelo site [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Instale a extensão [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) acessando a Abertura Rápida (Ctrl+P) e executando: 

  ```
  ext install msazurermtools.azurerm-vscode-tools
  ```

3. Reinicie o Código VS quando solicitado para habilitar a extensão.

## <a name="create-blank-template"></a>Criar o modelo em branco

Vamos começar com um modelo em branco que inclui apenas as seções básicas de um modelo.

1. Crie um arquivo. 

2. Copie e cole a seguinte sintaxe JSON em seu arquivo:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
  }
   ```

3. Salve este arquivo como **azuredeploy.json**. 

## <a name="add-storage-account"></a>Nova conta de armazenamento
1. Para definir uma conta de armazenamento para a implantação, adicione essa conta de armazenamento à seção **resources** de seu modelo. Para localizar os valores disponíveis para a conta de armazenamento, examine a [referência do modelo das contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts). Copie o JSON exibido para a conta de armazenamento. 

3. Cole esse JSON na seção **resources** do modelo, conforme mostra o exemplo a seguir: 

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [
      {
        "name": "string",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2016-05-01",
        "sku": {
          "name": "string"
        },
        "kind": "string",
        "location": "string",
        "tags": {},
        "properties": {
          "customDomain": {
            "name": "string",
            "useSubDomain": boolean
          },
          "encryption": {
            "services": {
              "blob": {
                "enabled": boolean
              }
            },
            "keySource": "Microsoft.Storage"
          },
          "accessTier": "string"
        }
      }
    ],
    "outputs": {  }
  }
  ```

  O exemplo anterior inclui muitos valores de espaço reservado e algumas propriedades que talvez você não precise em sua conta de armazenamento.

## <a name="set-values-for-storage-account"></a>Definir valores para a conta de armazenamento

Agora, você está pronto para definir valores para sua conta de armazenamento. 

1. Olhe novamente a [referência do modelo das contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts) onde você copiou o JSON. Há várias tabelas que descrevem as propriedades e fornecem os valores disponíveis. 

2. Observe que dentro do elemento **properties**, **customDomain**, **encryption**, e **accessTier** estão listados como não necessários. Esses valores podem ser importantes para seus cenários, mas para simplificar este exemplo, vamos removê-los.

  ```json
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "string",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  ```

3. No momento, o elemento **kind** é definido como um valor de espaço reservado ("string"). O VS Code inclui muitos recursos que ajudarão você a entender os valores a serem usados no modelo. Observe que o VS Code indica que esse valor não é válido. Se você passar por "string", o VS Code sugerirá que os valores válidos para **kind** são `Storage` ou `BlobStorage`. 

  ![mostrar os valores sugeridos do VS Code](./media/resource-manager-create-first-template/vs-code-show-values.png)

  Para ver os valores disponíveis, exclua os caracteres entre as aspas duplas e selecione **Ctrl + Espaço**. Selecione **Armazenamento** entre as opções disponíveis.
  
  ![mostrar intellisense](./media/resource-manager-create-first-template/intellisense.png)

  Se você não estiver usando o VS Code, examine a página de referência do modelo das contas de armazenamento. Observe que a descrição lista os mesmos valores válidos. Defina o elemento como **Armazenamento**.

  ```json
  "kind": "Storage",
  ```

O modelo agora se parece com:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Adicionar função de modelo

Use as funções em seu modelo para simplificar a sintaxe do modelo e para recuperar valores que estão disponíveis somente durante a implantação do modelo. Para obter a lista completa das funções de modelo, confira [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).

Para especificar a implantação da conta de armazenamento no mesmo local que o grupo de recursos, defina a propriedade **location**:

```json
"location": "[resourceGroup().location]",
```

Novamente, o VS Code ajuda você sugerindo as funções disponíveis. 

![mostrar funções](./media/resource-manager-create-first-template/show-functions.png)

Observe que a função é delimitada por colchetes. A função [resourceGroup](resource-group-template-functions.md#resourcegroup) retorna um objeto com uma propriedade chamada `location`. O grupo de recursos contém todos os recursos relacionados para sua solução. Você pode codificar a propriedade location para um valor como "EUA Central", mas precisaria alterar manualmente o modelo reimplantar em um local diferente. A função `resourceGroup` facilita a reimplantação desse modelo para um grupo de recursos diferente em um local diferente.



O modelo agora se parece com:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
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

## <a name="add-parameters-and-variables"></a>Adicionar parâmetros e variáveis
Há somente dois valores restantes para definir em seu modelo - **name** e **sku.name**. Para essas propriedades, adicione parâmetros que permitem a personalização desses valores durante a implantação. 

Os nomes de conta de armazenamento têm várias restrições que os tornam difíceis de definir. O nome deve ter entre três e 24 caracteres de comprimento e usar somente números e letras minúsculas, além de ser exclusivo. Em vez de tentar adivinhar um valor exclusivo que corresponda às restrições, use a função [uniqueString](resource-group-template-functions.md#uniquestring) para gerar um valor de hash. Para dar mais significado a esse valor de hash, adicione um prefixo que ajuda a identificá-lo como uma conta de armazenamento após a implantação. 

1. Para passar um prefixo para o nome que corresponda às suas convenções de nomenclatura, acesse a seção **parameters** de seu modelo. Adicione um parâmetro para o modelo que aceite um prefixo para o nome da conta de armazenamento:

  ```json
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
  },
  ```

  O prefixo é limitado a no máximo 11 caracteres, pois `uniqueString` retorna 13 caracteres, e o nome não pode exceder 24 caracteres. Se você não passar um valor para o parâmetro durante a implantação, o valor padrão será usado.

2. Acesse a seção **variables** do modelo. Para construir o nome a partir do prefixo e da string exclusiva, adicione a seguinte variável:

  ```json
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  ```

3. Na seção **resources**, defina o nome da conta de armazenamento para essa variável.

  ```json
  "name": "[variables('storageName')]",
  ```

3. Para habilitar a passagem em SKUs diferentes para a conta de armazenamento, acesse a seção **parameters**. Após o parâmetro do prefixo de nome do armazenamento, adicione um parâmetro que especifica os valores de SKU permitidos e um valor padrão. Encontre os valores permitidos na página de referência do modelo ou no VS Code. No exemplo a seguir, inclua todos os valores válidos para SKU. Porém, você pode limitar os valores permitidos apenas aos tipos de SKUs que você deseja implantar por meio desse modelo.

  ```json
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
  ```

3. Altere a propriedade do SKU para usar o valor do parâmetro:

  ```json
  "sku": {
    "name": "[parameters('storageSKU')]"
  },
  ```    

4. Salve o arquivo.

O modelo agora se parece com:

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
* O modelo está concluído, e você está pronto para implantá-lo em sua assinatura. Para implantar, confira [Implantar recursos no Azure](resource-manager-quickstart-deploy.md).
* Para saber mais sobre a estrutura de um modelo, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

