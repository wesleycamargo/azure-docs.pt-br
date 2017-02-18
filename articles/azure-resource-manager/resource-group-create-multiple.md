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
ms.date: 11/02/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: b3972f3d407b3ba9529b36005c0856796c272095


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Implantar várias instâncias de recursos nos modelos do Azure Resource Manager
Este tópico mostra como iterar em seu modelo do Gerenciador de Recursos do Azure para criar várias instâncias de um recurso.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex e length
No recurso a ser criado várias vezes, você pode definir um objeto **copy** que especifica o número de vezes para iterar. A cópia recebe o seguinte formato:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Você pode acessar o valor de iteração atual com a função **copyIndex()**. O exemplo a seguir usa copyIndex com a função concat para construir um nome.

    [concat('examplecopy-', copyIndex())]

Ao criar vários recursos de uma matriz de valores, você pode usar a função **length** para especificar a contagem. Você pode fornecer a matriz como o parâmetro para a função length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Só é possível aplicar o objeto de cópia para um recurso de nível superior. Você não pode aplicá-la a uma propriedade em um tipo de recurso ou a um recurso filho. No entanto, este tópico mostra como especificar vários itens de uma propriedade e criar várias instâncias de um recurso filho. A pseudocódigo de exemplo a seguir mostra onde a cópia pode ser aplicada:

    "resources": [
      {
        "type": "{provider-namespace-and-type}",
        "name": "parentResource",
        "copy": {  
          /* yes, copy can be applied here */
        },
        "properties": {
          "exampleProperty": {
            /* no, copy cannot be applied here */
          }
        },
        "resources": [
          {
            "type": "{provider-type}",
            "name": "childResource",
            /* copy can be applied if resource is promoted to top level */ 
          }
        ]
      }
    ] 

Embora você não possa aplicar a **copy** a uma propriedade, essa propriedade ainda faz parte das iterações do recurso que contém a propriedade. Portanto, você pode usar **copyIndex()** dentro da propriedade para especificar valores.

Há várias situações em que talvez você queira iterar em uma propriedade em um recurso. Por exemplo, talvez você queira especificar vários discos de dados para uma máquina virtual. Para ver como iterar em uma propriedade, veja [Criar várias instâncias quando a cópia não funciona](#create-multiple-instances-when-copy-wont-work). 

Para trabalhar com recursos filho, consulte [Criar várias instâncias de um recurso filho](#create-multiple-instances-of-a-child-resource).

## <a name="use-index-value-in-name"></a>Use o valor de índice no nome
Você pode usar a operação de cópia para criar várias instâncias de um recurso nomeadas exclusivamente com base no índice de incremento. Por exemplo, pode ser útil adicionar um número exclusivo no final de cada nome de recurso que é implantado. Para implantar três sites da Web nomeados:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Use o modelo a seguir:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

## <a name="offset-index-value"></a>Valor de índice de deslocamento
No exemplo anterior, o valor de índice vai de zero a 2. Para deslocar o valor do índice, você pode passar um valor da função **copyIndex()**, como **copyIndex(1)**. O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Desta maneira, usar o mesmo modelo do exemplo anterior, porém especificando **copyIndex(1)** , implantaria três sites da Web chamados:

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>Usar cópia com matriz
A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Para implantar três sites da Web nomeados:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Use o modelo a seguir:

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

É claro que você pode definir a contagem de cópias como um valor diferente do comprimento da matriz. Por exemplo, você poderia criar uma matriz com vários valores e, em seguida, passar um valor de parâmetro que especifique quantos dos elementos da matriz serão implantados. Nesse caso, você define a contagem de cópias como mostrado no primeiro exemplo. 

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em um loop
Você pode especificar a implantação de um recurso após outro recurso usando o elemento **dependsOn**. Para implantar um recurso que depende do conjunto de recursos em um loop, forneça o nome do loop de cópia no elemento **dependsOn**. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina Virtual. A definição completa da máquina Virtual não é exibida. Observe que o elemento de cópia tem **name** definido como **storagecopy** e o elemento **dependsOn** para as máquinas virtuais também é definido como **storagecopy**.

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

## <a name="create-multiple-instances-of-a-child-resource"></a>Criar diversas instâncias de um recurso filho
Você não pode usar um loop de cópia para um recurso filho. Para criar várias instâncias de um recurso que você normalmente define como aninhado dentro de outro recurso, você deve criar esse recurso como um recurso de nível superior. Definir a relação com o recurso pai por meio das propriedades **type** e **name**.

Por exemplo, suponha que você normalmente defina um conjunto de dados como um recurso filho em uma data factory.

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

Para criar várias instâncias de conjuntos de dados, mova-os para fora da data factory. O conjunto de dados deve estar no mesmo nível que a data factory, mas ainda é um recurso filho da data factory. Preserve a relação entre um conjunto de dados e a data factory por meio das propriedades **type** e **name**. Como o tipo não pode ser inferido de sua posição no modelo, você deve fornecer o tipo totalmente qualificado no seguinte formato:

 **{namespace-do-provedor-de-recursos}/{tipo-do-recurso-pai}/{tipo-do-recurso-filho}** 

Para estabelecer uma relação pai/filho com uma instância da data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso pai. Use o seguinte formato para o nome:

**{nome-do-recurso-pai}/{nome-do-recurso-filho}**.  

O exemplo a seguir mostra a implementação:

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

## <a name="create-multiple-instances-when-copy-wont-work"></a>Criar várias instâncias quando a cópia não funciona
Você só pode usar **cópia** em tipos de recursos, não propriedades dentro de um tipo de recurso. Esse requisito pode criar problemas para você quando quiser criar várias instâncias de algo que faz parte de um recurso. Um cenário comum é criar vários discos de dados para uma Máquina Virtual. Não é possível usar **copy** com os discos de dados porque **dataDisks** é uma propriedade na Máquina Virtual e não seu próprio tipo de recurso. Em vez disso, você cria uma matriz com tantos discos de dados conforme o necessário e passa o número real de discos de dados para criar. Na definição de máquina virtual, você deve usar a função **take** para obter apenas o número de elementos que você realmente deseja da matriz.

Um exemplo completo desse padrão é mostrado no modelo [Criar uma máquina virtual com uma seleção dinâmica de discos de dados](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

As seções relevantes do modelo de implantação são mostradas no exemplo a seguir. Grande parte do modelo foi removido para destacar as seções envolvidas na criação dinâmica de vários discos de dados. Observe o parâmetro **numDataDisks** que permite que você passe o número de discos para criar. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

Você pode usar a função **take** e o elemento **copy** juntos quando precisa criar várias instâncias de um recurso com um número variável de itens para uma propriedade. Por exemplo, suponha que você precisa criar várias máquinas virtuais, mas cada máquina virtual tem um número diferente de discos de dados. Para dar a cada disco de dados um nome que identifica a máquina virtual associada, coloque sua matriz de discos de dados em um modelo separado. Inclua parâmetros para o nome da máquina virtual e o número de discos de dados para retornar. Na seção de saídas, retorne o número de itens especificados.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

No modelo pai, você pode incluir parâmetros para o número de máquinas virtuais e uma matriz para o número de discos de dados para cada máquina virtual.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

Na seção de recursos, implante várias instâncias do modelo que define os discos de dados. 

```
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

Na seção de recursos, implante várias instâncias da máquina virtual. Para os discos de dados, consulte a implantação aninhada que contém o número correto e os nomes corretos dos discos de dados.

```
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>Valores retornados de um loop
Embora seja prático criar várias instâncias de um tipo de recurso, retornar valores desse loop pode ser difícil. Uma maneira de manter e retornar valores é usar **copy** com um modelo aninhado e fazer uma viagem de ida e volta de uma matriz que contenha todos os valores a retornar. Por exemplo, suponha que você queira criar várias contas de armazenamento e retornar o ponto de extremidade primário para cada uma delas. 

Primeiro, crie o modelo aninhado que cria a conta de armazenamento. Observe que ela aceita um parâmetro de matriz para os URIs de blob. Use esse parâmetro para fazer viagem de ida e volta de todos os valores de implantações anteriores. A saída do modelo é uma matriz que concatena o novo URI de blob aos URIs anteriores.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

Agora, crie o modelo pai que tem uma instância estática do modelo aninhado e faz loop nas instâncias restantes do modelo aninhado. Para cada instância da implantação em loop, passe uma matriz que é a saída da implantação anterior.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre as seções de um modelo, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para ver todas as funções que você pode usar em um modelo, veja [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).




<!--HONumber=Jan17_HO4-->


