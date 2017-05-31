---
title: "Implantar várias instâncias dos recursos do Azure | Microsoft Docs"
description: "Use a operação de cópia e matrizes em um modelo do Gerenciador de Recursos do Azure para iterar várias vezes durante a implantação de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a8e35456af8c9f2cf1bf9e9c364e33641f29a477
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Implantar várias instâncias de um recurso ou propriedade nos modelos do Azure Resource Manager
Este tópico mostra como iterar em seu modelo do Gerenciador de Recursos do Azure para criar várias instâncias de um recurso.

## <a name="resource-iteration"></a>Iteração de recurso
Para criar várias instâncias de um tipo de recurso, adicione um elemento `copy` ao tipo de recurso. No elemento de cópia, você deve especificar o número de iterações e um nome para esse loop. O valor da contagem deve ser um número inteiro positivo e não pode exceder 800. O Resource Manager cria os recursos em paralelo. Portanto, a ordem na qual eles são criados não é garantida. Para criar recursos iterados em sequência, consulte [Cópia serial](#serial-copy). 

O recurso para criar várias vezes recebe o seguinte formato:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Observe que o nome de cada recurso inclui a função `copyIndex()`, que retorna a iteração atual no loop. `copyIndex()`é baseado em zero. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* storage0
* storage1
* storage2.

Para deslocar o valor do índice, você pode passar um valor na função copyIndex(). O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Use a função `length` na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz. Assim, o seguinte exemplo:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Cria estes nomes:

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>Cópia serial

Ao usar o elemento de cópia para criar várias instâncias de um tipo de recurso, o Resource Manager, por padrão, implanta essas instâncias em paralelo. No entanto, convém especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, convém balancear as atualizações para que apenas um determinado número seja atualizado por vez.

O Resource Manager fornece propriedades do elemento de cópia que permitem que você implante várias instâncias em série. No elemento de cópia, defina `mode` como **serial** e `batchSize` como o número de instâncias a implantar por vez. Com o modo serial, o Resource Manager cria uma dependência em instâncias anteriores no loop de modo que não inicie um lote até que o lote anterior esteja concluído.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

A propriedade de modo também aceita **paralelo**, que é o valor padrão.

Para testar a cópia serial sem criar recursos reais, use o seguinte modelo que implanta modelos aninhados vazios:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

No histórico de implantação, observe que as implantações aninhadas são processadas em sequência.

![implantação serial](./media/resource-group-create-multiple/serial-copy.png)

Para um cenário mais realista, o exemplo a seguir implanta duas instâncias por vez de uma VM Linux de um modelo aninhado:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em um loop
Você especifica que um recurso é implantado após outro recurso usando o elemento `dependsOn`. Para implantar um recurso que depende da coleção de recursos em um loop, forneça o nome do loop de cópia no elemento dependsOn. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina Virtual. A definição completa da máquina Virtual não é exibida. Observe que o elemento de cópia tem o nome definido como `storagecopy` e o elemento dependsOn para as Máquinas Virtuais também é definido como `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Criar diversas instâncias de um recurso filho
Você não pode usar um loop de cópia para um recurso filho. Para criar várias instâncias de um recurso que você normalmente define como aninhado dentro de outro recurso, você deve criar esse recurso como um recurso de nível superior. Você define a relação com o recurso pai por meio das propriedades de tipo e nome.

Por exemplo, suponha que você normalmente defina um conjunto de dados como um recurso filho em uma data factory.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Para criar várias instâncias de conjuntos de dados, mova-os para fora da data factory. O conjunto de dados deve estar no mesmo nível que a data factory, mas ainda é um recurso filho da data factory. Você preserva a relação entre um conjunto de dados e o data factory por meio das propriedades de tipo e nome. Como o tipo não pode ser inferido de sua posição no modelo, você deve fornecer o tipo totalmente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação pai/filho com uma instância da data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso pai. Use o formato: `{parent-resource-name}/{child-resource-name}`.  

O exemplo a seguir mostra a implementação:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre as seções de um modelo, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


