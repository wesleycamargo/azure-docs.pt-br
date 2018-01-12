---
title: "Variáveis do modelo do Azure Resource Manager | Microsoft Docs"
description: "Descreve como definir variáveis em modelos do Azure Resource Manager usando a sintaxe JSON declarativa."
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
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Seção de variáveis dos modelos do Azure Resource Manager
Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas.

## <a name="define-and-use-a-variable"></a>Definir e usar uma variável

O seguinte exemplo mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatená-lo como uma cadeia de caracteres exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Você usa a variável ao definir o recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Definições disponíveis

O exemplo anterior mostrou uma maneira de definir uma variável. Você pode usar qualquer uma das definições a seguir:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Variáveis de configuração

Você pode usar tipos JSON complexos a fim de definir valores relacionados para um ambiente. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

Em parâmetros, você cria um valor que indica quais valores de configuração devem ser usados.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Você recupera as configurações atuais com:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Usar elemento de cópia na definição de variável

Use a sintaxe de **cópia** para criar uma variável com uma matriz de vários elementos. Forneça uma contagem do número de elementos. Cada elemento contém as propriedades no objeto de **entrada**. Use a cópia em uma variável ou para criar a variável. Ao definir uma variável e usar **cópia** dentro dessa variável, você cria um objeto que tem uma propriedade de matriz. Quando você usa **cópia** no nível superior e defina uma ou mais variáveis dentro dele, você cria um ou mais conjuntos. Ambas as abordagens são mostradas no seguinte exemplo:

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

A variável **disk-array-on-object** contém o seguinte objeto com uma matriz chamada **disks**:

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

A variável **disks-top-level-array** contém a seguinte matriz:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Você também pode especificar mais de um objeto ao usar a cópia para criar variáveis. O exemplo a seguir define duas matrizes como variáveis. Uma é chamada **matriz de nível superior de discos** e tem cinco elementos. A outra é chamada **uma matriz diferente** e tem três elementos.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Essa abordagem funciona bem quando você precisa utilizar valores de parâmetro e se certificar de que estejam no formato correto para um valor de modelo. O exemplo a seguir formata valores de parâmetro para uso na definição de regras de segurança:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>Recomendações
As seguintes informações podem ser úteis quando você trabalha com variáveis:

* Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código facilita a leitura do modelo.
* Não é possível usar a função [reference](resource-group-template-functions-resource.md#reference) na seção de **variables** do modelo. A função **reference** deriva seu valor do estado de tempo de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que precisam da função **reference** diretamente na seção **resources** ou **outputs** do modelo.
* Inclua variáveis em nomes de recurso que devem ser exclusivos.

## <a name="example-templates"></a>Modelos de exemplo

Esses modelos de exemplo demonstram alguns cenários de uso de variáveis. Implante-os para testar como as variáveis são tratadas em cenários diferentes. 

|Modelo  |Descrição  |
|---------|---------|
| [definições disponíveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [arquivo de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Cria uma matriz no formato correto para a atribuição de regras de segurança a um grupo de segurança de rede. |


## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Essa situação é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).