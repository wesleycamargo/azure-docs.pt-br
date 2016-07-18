<properties
   pageTitle="Implantar várias instâncias dos recursos | Microsoft Azure"
   description="Use a operação de cópia e matrizes em um modelo do Gerenciador de Recursos do Azure para iterar várias vezes durante a implantação de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# Criar várias instâncias de recursos no Gerenciador de Recursos do Azure

Este tópico mostra como iterar em seu modelo do Gerenciador de Recursos do Azure para criar várias instâncias de um recurso.

## copy, copyIndex e length

No recurso a ser criado várias vezes, você pode definir um objeto **copy** que especifica o número de vezes para iterar. A cópia recebe o seguinte formato:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Você pode acessar o valor de iteração atual com a função **copyIndex()**, como mostrado a seguir na função concat.

    [concat('examplecopy-', copyIndex())]

Ao criar vários recursos de uma matriz de valores, você pode usar a função **length** para especificar a contagem. Você pode fornecer a matriz como o parâmetro para a função length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Use o valor de índice no nome

Você pode usar a operação de cópia para criar várias instâncias de um recurso nomeadas exclusivamente com base no índice de incremento. Por exemplo, pode ser útil adicionar um número exclusivo no final de cada nome de recurso que é implantado. Para implantar três sites da Web nomeados:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

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

## Valor de índice de deslocamento

Você pode observar no exemplo anterior que o valor de índice vai de zero a 2. Para deslocar o valor do índice, você pode passar um valor da função **copyIndex()**, como **copyIndex(1)**. O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Desta maneira, usar o mesmo modelo do exemplo anterior, porém especificando **copyIndex(1)**, implantaria três sites da Web chamados:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Usar cópia com matriz
   
A operação de cópia é especialmente útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Para implantar três sites da Web nomeados:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

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

É claro que você define a contagem de cópias como um valor diferente do comprimento da matriz. Por exemplo, você poderia criar uma matriz com vários valores e, em seguida, passar um valor de parâmetro que especifique quantos dos elementos da matriz serão implantados. Nesse caso, você define a contagem de cópias como mostrado no primeiro exemplo.

## Dependendo dos recursos em um loop

Você pode especificar a implantação de um recurso após outro recurso usando o elemento **dependsOn**. Quando você precisa implantar um recurso que depende do conjunto de recursos em um loop, é possível fornecer o nome do loop de cópia no elemento **dependsOn**. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina Virtual. A definição completa da máquina Virtual não é exibida. Observe que o elemento de cópia tem o **nome** definido como **storagecopy** e o elemento **dependsOn** para as máquinas virtuais também é definido como **storagecopy**.

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

## Looping em um recurso aninhado

Você não pode usar um loop de cópia para um recurso aninhado. Se você precisar criar várias instâncias de um recurso que normalmente é definido como aninhado em outro recurso, deverá, em vez disso, criar o recurso como um recurso de nível superior e definir a relação com o recurso pai por meio das propriedades **tipo** e **nome**.

Por exemplo, suponha que você normalmente defina um conjunto de dados como um recurso aninhado em uma Data Factory.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Para criar várias instâncias de conjuntos de dados, você precisa alterar o modelo, conforme mostrado abaixo. Observe que o tipo totalmente qualificado e o nome incluem o nome da data factory.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## Criar várias instâncias quando a cópia não funciona

Você só pode usar **cópia** em tipos de recursos, não propriedades dentro de um tipo de recurso. Isso pode criar problemas para você quando quiser criar várias instâncias de algo que faz parte de um recurso. Um cenário comum é criar vários discos de dados para uma Máquina Virtual. Não é possível usar **copy** com os discos de dados porque **dataDisks** é uma propriedade na Máquina Virtual e não seu próprio tipo de recurso. Em vez disso, você cria uma matriz com tantos discos de dados conforme o necessário e passa o número real de discos de dados para criar. Na definição de máquina virtual, você deve usar a função **take** para obter apenas o número de elementos que você realmente deseja da matriz.

Um exemplo completo desse padrão é mostrado no modelo [Criar uma máquina virtual com uma seleção dinâmica de discos de dados](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/).

As seções relevantes do modelo de implantação são mostradas abaixo. Grande parte do modelo foi removido para destacar as seções envolvidas na criação dinâmica de vários discos de dados. Observe o parâmetro **numDataDisks** que permite que você passe o número de discos para criar.

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


## Próximas etapas
- Para saber mais sobre as seções de um modelo, confira [Criando modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).
- Para ver todas as funções que você pode usar em um modelo, confira [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md).
- Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0706_2016-->