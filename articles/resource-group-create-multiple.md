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
   ms.date="08/27/2015"
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
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Valor de índice de deslocamento

Você pode observar no exemplo anterior que o valor de índice vai de zero a 2. Para deslocar o valor do índice, você pode passar um valor da função **copyIndex()**, como **copyIndex(1)**. O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Desta maneira, usar o mesmo modelo do exemplo anterior, porém especificando **copyIndex(1)**, implantaria três sites da Web chamados:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Use com matriz
   
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
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {} 
      } 
    ]

É claro que você define a contagem de cópias como um valor diferente do comprimento da matriz. Por exemplo, você poderia criar uma matriz com vários valores e, em seguida, passar um valor de parâmetro que especifique quantos dos elementos da matriz serão implantados. Nesse caso, você define a contagem de cópias como mostrado no primeiro exemplo.

## Próximas etapas
- Para saber mais sobre as seções de um modelo, confira [Criando modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).
- Para ver todas as funções que você pode usar em um modelo, confira [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md).
- Para saber mais sobre como implantar modelos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](azure-portal/resource-group-template-deploy.md).

<!---HONumber=Oct15_HO3-->