---
title: "Funções do modelo do Azure Resource Manager – numéricas | Microsoft Docs"
description: "Descreve as funções a serem usadas em um modelo do Resource Manager para trabalhar com números."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 5844540801a6f0ff593b3f404f6815473c65a52e
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funções numéricas para modelos do Azure Resource Manager

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com números inteiros:

* [adicionar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>Adicionar
`add(operand1, operand2)`

Retorna a soma dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- | 
|operand1 |Sim |int |Primeiro número a ser adicionado. |
|operand2 |Sim |int |Segundo número a ser adicionado. |

### <a name="examples"></a>Exemplos

O exemplo a seguir adiciona dois parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que contém a soma dos parâmetros.

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Retorna o índice de um loop de iteração. 

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| deslocamento |Não |int |O número a ser adicionado ao valor de iteração com base em zero. |

### <a name="remarks"></a>Comentários

Essa função é sempre usada com um objeto **copy** . Se nenhum valor for fornecido para **offset**, o valor de iteração atual retornará. O valor de iteração começa em zero. Para obter uma descrição completa de como usar **copyIndex**, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra um loop de cópia e o valor de índice incluído no nome. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o índice atual da iteração.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Retorna a divisão de inteiros dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número que está sendo dividido. |
| operand2 |Sim |int |O número usado para dividir. Não pode ser 0. |

### <a name="examples"></a>Exemplos

O exemplo a seguir divide um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a divisão.

<a id="float" />

## <a name="float"></a>flutuante
`float(arg1)`

Converte o valor em um número de ponto flutuante. Você só usa essa função ao passar parâmetros personalizados para um aplicativo, como um aplicativo lógico.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |string ou int |O valor a ser convertido em um número de ponto flutuante. |

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra como usar float para passar parâmetros para um aplicativo lógico:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>Valor de retorno
Um número de ponto flutuante.

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Converte o valor especificado em um inteiro.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |string ou int |O valor a ser convertido em um inteiro. |

### <a name="examples"></a>Exemplos

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro.

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Retorna o valor mínimo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor mínimo. |

### <a name="examples"></a>Exemplos

O seguinte exemplo mostra como usar min com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor mínimo da coleção.

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Retorna o valor máximo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor máximo. |

### <a name="examples"></a>Exemplos

O seguinte exemplo mostra como usar max com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor máximo da coleção.

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número que está sendo dividido. |
| operand2 |Sim |int |O número usado para dividir. Não pode ser 0. |

### <a name="examples"></a>Exemplos

O exemplo a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno
Um inteiro que representa o resto.

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Retorna a multiplicação de dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |Primeiro número a ser multiplicado. |
| operand2 |Sim |int |Segundo número a ser multiplicado. |

### <a name="examples"></a>Exemplos

O exemplo a seguir multiplica um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a multiplicação.

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Retorna a subtração dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número do qual é subtraído. |
| operand2 |Sim |int |O número subtraído. |

### <a name="examples"></a>Exemplos

O exemplo a seguir subtrai um parâmetro de outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno
Um inteiro que representa a subtração.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


