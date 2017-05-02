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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Implantar várias instâncias de recursos nos modelos do Azure Resource Manager
Este tópico mostra como iterar em seu modelo do Gerenciador de Recursos do Azure para criar várias instâncias de um recurso.

## <a name="copy-and-copyindex"></a>copy e copyIndex
O recurso para criar várias vezes recebe o seguinte formato:

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Observe que o número de vezes para iterar é especificado no objeto de cópia:

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

O valor da contagem deve ser um número inteiro positivo e não pode exceder 800.

Observe que o nome de cada recurso inclui a função `copyIndex()`, que retorna a iteração atual no loop.

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

Se você implantar três sites da Web, eles serão nomeados:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Para deslocar o valor do índice, você pode passar um valor na função copyIndex(), como `copyIndex(1)`. O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Desta maneira, usar o mesmo modelo do exemplo anterior, mas especificando copyIndex(1), implantaria três sites da Web chamados:

* examplecopy-1
* examplecopy-2
* examplecopy-3

O Resource Manager cria os recursos em paralelo. Portanto, a ordem na qual eles são criados não é garantida. Para criar recursos repetidos em sequência, consulte [Loop sequencial para modelos do Azure Resource Manager](resource-manager-sequential-loop.md). 

Só é possível aplicar o objeto de cópia para um recurso de nível superior. Você não pode aplicá-la a uma propriedade em um tipo de recurso ou a um recurso filho. A pseudocódigo de exemplo a seguir mostra onde a cópia pode ser aplicada:

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

Para iterar um recurso filho, consulte [Criar várias instâncias de um recurso filho](#create-multiple-instances-of-a-child-resource).

Embora você não possa aplicar a cópia a uma propriedade, essa propriedade ainda faz parte das iterações do recurso que contém a propriedade. Portanto, você pode usar copyIndex() dentro da propriedade para especificar valores. Para criar vários valores para uma propriedade, consulte [Criar várias instâncias da propriedade no tipo de recurso](resource-manager-property-copy.md).

## <a name="use-copy-with-array"></a>Usar cópia com matriz
A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Para implantar três sites da Web nomeados:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Use o modelo a seguir:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Observe que a função `length` é usada para especificar a contagem. Você pode fornecer a matriz como o parâmetro para a função length.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
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
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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
* Para criar recursos repetidos em sequência, consulte [Loop sequencial para modelos do Azure Resource Manager](resource-manager-sequential-loop.md).
* Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


