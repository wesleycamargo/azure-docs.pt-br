---
title: "Funções de modelo do Azure Resource Manager – comparação | Microsoft Docs"
description: "Descreve as funções a serem usadas em um modelo do Resource Manager para comparar valores."
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
ms.openlocfilehash: 7f19efa7e09b0dce43851019f94285b2887c46d5
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Funções de comparação para modelos do Azure Resource Manager

O Resource Manager fornece várias funções para fazer comparações em seus modelos.

* [equals](#equals)
* [less](#less)
* [lessOrEquals](#lessorequals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)

<a id="equals" />

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Verifica se dois valores são iguais entre si.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para verificar a igualdade. |
| arg2 |Sim |int, string, array ou object |O segundo valor para verificar a igualdade. |

### <a name="examples"></a>Exemplos

O modelo de exemplo verifica os diferentes tipos de valores para igualdade. Todos os valores padrão retornam True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Retorna **True** se os valores são iguais; caso contrário, **False**.

<a id="less" />

## <a name="less"></a>less
`less(arg1, arg2)`

Verifica se o primeiro valor é menor que o segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que. |

### <a name="examples"></a>Exemplos

O modelo de exemplo verifica se um valor é menor que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é menor que o segundo valor; caso contrário, **False**.

<a id="lessorequals" />

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é menor que ou igual ao segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que ou igual a. |

### <a name="examples"></a>Exemplos

O modelo de exemplo verifica se um valor é menor que ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é menor que ou igual ao segundo valor; caso contrário, **False**.

<a id="greater" />

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Verifica se o primeiro valor é maior que o segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que. |

### <a name="examples"></a>Exemplos

O modelo de exemplo verifica se um valor é maior que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é maior que o segundo valor; caso contrário, **False**.

<a id="greaterorequals" />

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior que ou igual ao segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que ou igual a. |

### <a name="examples"></a>Exemplos

O modelo de exemplo verifica se um valor é maior que ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é maior que ou igual ao segundo valor; caso contrário, **False**.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


