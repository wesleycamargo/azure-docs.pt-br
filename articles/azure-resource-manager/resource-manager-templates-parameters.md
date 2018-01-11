---
title: "Seção de parâmetros de modelo do Azure Resource Manager | Microsoft Docs"
description: "Descreve a seção de parâmetros dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Seção de parâmetros dos modelos do Azure Resource Manager
Na seção de parâmetros do modelo, você deve especificar os valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem personalizar a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros o modelo sempre implantaria os mesmos recursos com o mesmo nomes, locais e propriedades.

Você está limitado a 255 parâmetros em um modelo. Você pode reduzir o número de parâmetros por meio de objetos que contenham várias propriedades, como é mostrado neste artigo.

## <a name="define-and-use-a-parameter"></a>Definir e usar um parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Ele define o nome do parâmetro e especifica que leva um valor de cadeia de caracteres. O parâmetro aceita apenas valores que fazem sentido para o uso pretendido. Especifica um valor padrão quando nenhum valor é fornecido durante a implantação. Por fim, o parâmetro inclui uma descrição de seu uso. 

```json
"parameters": {
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
}
```

No modelo, você referencia o valor para o parâmetro com a seguinte sintaxe:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>Propriedades disponíveis

O exemplo anterior mostrou apenas algumas das propriedades que você pode usar na seção de parâmetros. As propriedades disponíveis são:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| parameterName |Sim |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| type |Sim |Tipo do valor do parâmetro. Os valores e tipos permitidos são **cadeia de caracteres**, **secureString**, **int**, **bool**, **objeto**, **secureObject**, e **matriz**. |
| defaultValue |Não |Valor padrão do parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |Não |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |Não |O comprimento mínimo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo. |
| maxLength |Não |O comprimento máximo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo. |
| Descrição |Não |Descrição do parâmetro exibido aos usuários pelo portal. |

## <a name="template-functions-with-parameters"></a>Funções com parâmetros de modelo

Ao fornecer o valor padrão para um parâmetro, você pode usar a maioria das funções de modelo. Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir demonstra o uso de funções no valor padrão:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Não é possível usar a função `reference` na seção de parâmetros. Os parâmetros são avaliados antes da implantação, portanto a função `reference` não pode obter o estado de tempo de execução de um recurso. 

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados, passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

Defina o parâmetro no modelo e especifique um objeto JSON em vez de um único valor durante a implantação. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Em seguida, faça referência às subpropriedades do parâmetro usando o operador ponto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location":"[resourceGroup().location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Recomendações
As seguintes informações podem ser úteis quando você trabalha com parâmetros:

* Minimize o uso de parâmetros. Sempre que possível, use uma variável ou um valor literal. Use parâmetros somente para estes cenários:
   
   * Configurações em que queira usar variações de acordo com o ambiente (SKU, tamanho e capacidade).
   * Nomes de recurso que queira especificar para fácil identificação.
   * Valores que usa com frequência para concluir outras tarefas (como um nome de usuário de administrador).
   * Segredos (como senhas).
   * O número ou a matriz de valores a ser usado durante a criação de várias instâncias de um tipo de recurso.
* Use minúsculas concatenadas para nomes de parâmetro.
* Forneça uma descrição de cada parâmetro nos metadados:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina valores padrão para parâmetros (exceto senhas e chaves SSH). Fornecendo um valor padrão, o parâmetro torna-se opcional durante a implantação. O valor padrão pode ser uma cadeia de caracteres vazia. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Use **SecureString** para todos os segredos e senhas. Se você passar dados confidenciais em um objeto JSON, use o tipo **secureObject**. Os parâmetros de modelo com o tipo secureString ou secureObject não podem ser lidos após a implantação de recursos. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Sempre que possível, não use um parâmetro para especificar o local. Em vez disso, use a propriedade **location** do grupo de recursos. Ao usar a expressão **resourceGroup().location** para todos os seus recursos, os recursos no modelo são implantados no mesmo local do grupo de recursos:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Se um tipo de recurso tiver suporte em apenas um número limitado de locais, talvez seja conveniente especificar um local válido diretamente no modelo. Se você precisar usar um parâmetro **location**, compartilhe esse valor de parâmetro o máximo possível com os recursos que provavelmente estarão no mesmo local. Essa abordagem minimiza o número de vezes que os usuários são solicitados a fornecer informações de local.
* Evite usar um parâmetro ou variável para a versão de API de um tipo de recurso. Os valores e propriedades do recurso podem variar de acordo com o número de versão. O IntelliSense em um editor de código não pode determinar o esquema correto quando a versão da API é definida como um parâmetro ou uma variável. Em vez disso, codifique a versão da API no modelo.
* Evite especificar um nome de parâmetro no modelo que corresponde a um parâmetro no comando de implantação. O Resource Manager resolve esse conflito de nomeação adicionando o sufixo **FromTemplate** ao parâmetro do modelo. Por exemplo, se você incluir um parâmetro chamado **ResourceGroupName** em seu modelo, ele entrará em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Durante a implantação, você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Modelos de exemplo

Esses modelos de exemplo demonstram alguns cenários de uso de parâmetros. Implante-os para testar como os parâmetros são tratados em cenários diferentes.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções de valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar funções de modelo ao definir valores padrão para parâmetros. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra como usar um objeto para um parâmetro. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |

## <a name="next-steps"></a>Próximas etapas

* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para saber como inserir os valores do parâmetro durante a implantação, consulte [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md). 
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para obter informações sobre como usar um objeto de parâmetro, consulte [usar um objeto como um parâmetro em um modelo do Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).