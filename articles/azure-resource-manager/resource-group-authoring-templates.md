---
title: Estrutura e sintaxe do modelo do Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: b0bc5abd768be0fa5876aaef108cd71a15d94510
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager
Este artigo descreve a estrutura de um modelo do Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que podem ser usados na criação de valores para sua implantação. Para ver um tutorial passo a passo sobre como criar um modelo, confira [Criar seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de modelo
Em sua estrutura mais simples, um modelo contém os seguintes elementos:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| $schema |sim |Local do arquivo de esquema JSON que descreve a versão da linguagem do modelo. Use a URL mostrada no exemplo anterior. |
| contentVersion |sim |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado. |
| parâmetros |Não  |Valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos. |
| variáveis |Não  |Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo. |
| recursos |sim |Tipos de recursos que são implantados ou atualizados em um grupo de recursos. |
| outputs |Não  |Valores que são retornados após a implantação. |

Cada elemento contém propriedades que você pode definir. O seguinte exemplo contém a sintaxe completa de um modelo:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
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
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Esse artigo descreve as seções do modelo com mais detalhes.

## <a name="syntax"></a>Sintaxe
A sintaxe básica do modelo é JSON. No entanto, as expressões e as funções estendem os valores JSON disponíveis no modelo.  As expressões são escritas em literais de cadeia de caracteres JSON cujo primeiro e último caracteres são os colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Embora gravado como um literal de cadeia de caracteres, o resultado da avaliação da expressão pode ser de um tipo JSON diferente, como uma matriz ou um inteiro, dependendo da expressão real.  Para ter uma cadeia de caracteres literal que começa com um colchete `[`, mas que não é interpretada como uma expressão, adicione um colchete extra para iniciar a cadeia de caracteres com `[[`.

Normalmente, você usa expressões com funções para executar operações e configurar a implantação. Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. Você faz referência às propriedades usando os operadores dot e [index].

O seguinte exemplo mostra como usar várias das funções ao construir um valor:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>parâmetros
Na seção de parâmetros do modelo, você deve especificar os valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem personalizar a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros o modelo sempre implantaria os mesmos recursos com o mesmo nomes, locais e propriedades.

O exemplo a seguir mostra uma definição de parâmetro simples:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Para obter informações sobre como definir parâmetros, consulte a [seção de Parâmetros de modelos do Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>variáveis
Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas.

O seguinte exemplo mostra uma definição simples de variável:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Para obter informações sobre como definir variáveis, consulte a [seção de Variáveis de modelos do Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="resources"></a>Recursos
Na seção de recursos, você define os recursos que são implantados ou atualizados. Essa seção pode ficar mais complicada, porque você precisa entender os tipos que está implantando para fornecer os valores corretos.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Para obter mais informações, consulte a [seção Recursos de modelos do Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>outputs
Na seção de saídas, você especifica valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Para obter mais informações, consulte a [seção Saídas de modelos do Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho de seu modelo em 1 MB e cada arquivo de parâmetro em 64 KB. O limite de 1 MB se aplica para o estado final do modelo depois que ele foi expandido com definições de recurso iterativo e valores para variáveis e parâmetros. 

Você também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para saber mais, confira [Uso de modelos vinculados ao implantar recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Essa situação é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).
