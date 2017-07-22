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
ms.date: 06/13/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: d993bd5cae5fa2f0a6eda999c95b10bd1c268be2
ms.contentlocale: pt-br
ms.lasthandoff: 07/21/2017

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

### <a name="return-value"></a>Valor de retorno

Retorna **True** se os valores são iguais; caso contrário, **False**.

### <a name="remarks"></a>Comentários

A função equals é frequentemente usada com o elemento `condition` para testar se um recurso está implantado.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Exemplo

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True  |
| checkStrings | Bool | True  |
| checkArrays | Bool | True  |
| checkObjects | Bool | True  |

<a id="less" />

## <a name="less"></a>less
`less(arg1, arg2)`

Verifica se o primeiro valor é menor que o segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que. |

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é menor que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O modelo de exemplo verifica se um valor é menor que o outro.

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
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True  |
| checkStrings | Bool | Falso |

<a id="lessorequals" />

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é menor que ou igual ao segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que ou igual a. |

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é menor que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O modelo de exemplo verifica se um valor é menor que ou igual ao outro.

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
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True  |
| checkStrings | Bool | Falso |

<a id="greater" />

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Verifica se o primeiro valor é maior que o segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que. |

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é maior que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O modelo de exemplo verifica se um valor é maior que o outro.

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
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True  |

<a id="greaterorequals" />

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior que ou igual ao segundo valor.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que ou igual a. |

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o primeiro valor é maior que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O modelo de exemplo verifica se um valor é maior que ou igual ao outro.

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
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True  |


## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md).


