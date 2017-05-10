---
title: "Modelos do Azure Resource Manager – Objetos como parâmetros | Microsoft Docs"
description: "Descreve como estender a funcionalidade dos modelos do Azure Resource Manager para usar objetos como parâmetros"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0ab00cc3455d4bff7bfe1dfb62bafa550d65dea8
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Padrões para estender a funcionalidade dos modelos do Azure Resource Manager – objetos como parâmetros

Os modelos do Azure Resource Manager dão suporte a parâmetros para especificar valores, a fim de personalizar uma implantação de recurso. Embora esse recurso seja útil e permita a criação de implantações complexas, um único modelo é limitado a 255 parâmetros. Se você usar um parâmetro para cada propriedade em um recurso e tiver uma implantação grande, poderá não ter parâmetros suficientes.

## <a name="create-object-as-parameter"></a>Criar um objeto como parâmetro

Uma maneira de resolver esse problema é usar um objeto como parâmetro. O padrão é especificar um parâmetro como um objeto no modelo e, em seguida, fornecer o objeto como um valor ou uma matriz de valores. Consulte seus subpropriedades usando a função `parameter()` e o operador ponto. Por exemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
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
  ],          
  "outputs": {}
}

```

O arquivo de parâmetro correspondente é parecido com este:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

Neste exemplo, todos os valores especificados na VNet foram obtidos de um único parâmetro, `VNetSettings`. Esse padrão é útil para o gerenciamento de valores da propriedade, pois você mantém todos os valores de um recurso específico em um único objeto. Além disso, embora esse exemplo use um objeto como parâmetro, você também pode usar uma matriz de objetos como parâmetro. Consulte os objetos usando um índice na matriz.

## <a name="use-object-instead-of-multiple-arrays"></a>Usar um objeto em vez de várias matrizes

Você pode ter usado um padrão semelhante para criar várias instâncias de um recurso criando várias matrizes de valores de propriedade e iterando em cada matriz para selecionar o valor. Esse padrão funciona bem ao criar vários recursos do mesmo tipo, mas pode ser inconveniente quando usado para criar recursos filho. 

Há dois motivos para esse problema. Primeiro, o Resource Manager tenta implantar os recursos filho em paralelo, mas a implantação falha quando os dois recursos filho atualizam o pai simultaneamente. 

Em segundo lugar, cada matriz de valores da propriedade é iterada em paralelo e, se a forma de cada matriz não for a mesma, ocorrerá um erro. Por exemplo, considere as seguintes matrizes de propriedade:

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

O padrão típico para atribuir esses valores a propriedades dentro de um loop de cópia é acessar a propriedade usando a função `variables()` e usar `copyIndex()` como um índice na matriz. Por exemplo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
Observe que a `count` do loop de cópia se baseia no número de propriedades da matriz `firstProperty`. No entanto, não há o mesmo número de propriedades na matriz `secondProperty`. Há uma falha na validação nesse modelo porque o tamanho da matriz `secondProperty` não é o mesmo.

No entanto, se você incluir todas as propriedades em um único objeto, ficará muito mais fácil observar quando um valor está ausente. Por exemplo:

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="use-with-sequential-copy"></a>Usar com a cópia sequencial

Esse padrão é ainda mais útil quando combinado com o [padrão de cópia sequencial](resource-manager-sequential-loop.md), particularmente para implantar recursos filho. O modelo de exemplo a seguir implanta um NSG (grupo de segurança de rede) com duas regras de segurança. O primeiro recurso chamado `NSG1` implanta o NSG. O segundo grupo de recursos chamado `loop-0` executa duas funções: primeiro, ele usa `dependsOn` no NSG para que sua implantação só seja iniciada quando o `NSG1` for concluído e essa é a primeira iteração do loop sequencial. O terceiro recurso é um modelo aninhado que implanta as regras de segurança usando um objeto para seus valores de parâmetro como no último exemplo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
                        }
                  }
            ],
            "outputs": {}
          }
        }
    }
  ],          
  "outputs": {}
}

```

O arquivo de parâmetro correspondente é parecido com este:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

## <a name="try-the-template"></a>Experimentar o modelo

Para testar esses modelos, siga estas etapas:

1.    Visite o portal do Azure, selecione o ícone "+" e pesquise pelo tipo de recurso "implantação de modelo". Quando você o achar nos resultados da pesquisa, selecione-o.
2.    Quando chegar à página “Implantação de modelo”, selecione o botão **Criar**. Esse botão abre a folha “Implantação personalizada”.
3.    Selecione o botão **Editar modelo**.
4.    Exclua o modelo vazio no painel à direita.
5.    Copie e cole o modelo de exemplo no painel direito.
6.    Selecione o botão **Salvar**.
7.    Quando retornar ao painel de “implantação personalizada”, selecione o botão **Editar parâmetros**.
8.  Na folha “Editar parâmetros”, exclua o modelo existente.
9.  Copie e cole o modelo de parâmetro de exemplo acima.
10. Selecione o botão **Salvar**, que fará com que você retorne à folha “Implantação personalizada”.
11. Na folha “Implantação personalizada”, selecione sua assinatura, crie um novo grupo de recursos ou use um existente e selecione uma localização. Examine os termos e condições e selecione a caixa de seleção “Eu concordo”.
12.    Selecione o botão **Comprar**.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais do que o máximo de 255 parâmetros permitidos por implantação, use esse padrão para especificar menos parâmetros no modelo. Você também pode usar esse padrão para gerenciar as propriedades dos recursos mais facilmente e, depois, implantá-los sem conflitos usando o padrão de loop sequencial.

* Para obter uma introdução à função `parameter()` e ao uso de matrizes, consulte [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Esse padrão também é implementado no [projeto de blocos de construção do modelo](https://github.com/mspnp/template-building-blocks) e nas [arquiteturas de referência do Azure](/azure/architecture/reference-architectures/).
