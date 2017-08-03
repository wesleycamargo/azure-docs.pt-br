---
title: "Funções de modelo do Azure Resource Manager – matrizes e objetos | Microsoft Docs"
description: "Descreve as funções a serem usadas em um modelo do Azure Resource Manager para trabalhar com matrizes e objetos."
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
ms.date: 06/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 74982663b0501d3a5c7973a5f383e14e0f964696
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017


---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Funções de matriz e objeto para modelos do Azure Resource Manager 

O Resource Manager fornece diversas funções para trabalhar com matrizes e objetos.

* [array](#array)
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [min](#min)
* [max](#max)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Para obter uma matriz de valores de cadeia de caracteres delimitada por um valor, confira [split](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>array
`array(convertToArray)`

Converte o valor em uma matriz.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| convertToArray |Sim |int, string, array ou object |O valor a ser convertido em uma matriz. |

### <a name="return-value"></a>Valor de retorno

Uma matriz.

### <a name="example"></a>Exemplo

O seguinte exemplo mostra como usar a função array com tipos diferentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "a"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intOutput | Matriz | [1] |
| stringOutput | Matriz | ["a"] |
| objectOutput | Matriz | [{"a": "b", "c": "d"}] |

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

Retorna o primeiro valor não nulo dos parâmetros. Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para testar se é nulo. |
| argumentos adicionais |Não |int, string, array ou object |Valores adicionais para testar se são nulos. |

### <a name="return-value"></a>Valor de retorno

O valor dos primeiros parâmetros não nulos, que pode ser uma cadeia de caracteres, inteiro, matriz ou objeto. Null se todos os parâmetros forem nulos. 

### <a name="example"></a>Exemplo

O exemplo a seguir mostra a saída de diferentes usos de coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null, 
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | Cadeia de caracteres | padrão |
| intOutput | int | 1 |
| objectOutput | Objeto | {"first": "default"} |
| arrayOutput | Matriz | [1] |
| emptyOutput | Bool | True  |

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Combina várias matrizes e retorna a matriz concatenada, ou combina vários valores de cadeia de caracteres e retorna a matriz concatenada. 

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |A primeira matriz ou cadeia de caracteres para concatenação. |
| argumentos adicionais |Não |matriz ou cadeia de caracteres |Matrizes ou cadeias de caractere adicionais em ordem sequencial para concatenação. |

Essa função pode conter qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes como parâmetros.

### <a name="return-value"></a>Valor de retorno
Uma cadeia de caracteres ou matriz de valores concatenados.

### <a name="example"></a>Exemplo

O próximo exemplo mostra como combinar duas matrizes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| retorno | Matriz | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

O exemplo a seguir mostra como combinar dois valores de cadeia de caracteres e retornar uma cadeia de caracteres concatenada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | Cadeia de caracteres | prefix-5yj4yjf5mbg72 |

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave ou uma cadeia de caracteres contém uma subcadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contêiner |Sim |matriz, objeto ou cadeia de caracteres |O valor que contém o valor a ser encontrado. |
| itemToFind |Sim |string ou int |O valor a ser encontrado. |

### <a name="return-value"></a>Valor de retorno

**True** se o item for encontrado; caso contrário, **False**.

### <a name="example"></a>Exemplo

O seguinte exemplo mostra como usar contains com tipos diferentes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringTrue | Bool | True  |
| stringFalse | Bool | Falso |
| objectTrue | Bool | True  |
| objectFalse | Bool | Falso |
| arrayTrue | Bool | True  |
| arrayFalse | Bool | Falso |

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Cria uma matriz de parâmetros.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |String, Inteiro, Matriz ou Objeto |O primeiro valor na matriz. |
| argumentos adicionais |Não |String, Inteiro, Matriz ou Objeto |Valores adicionais na matriz. |

### <a name="return-value"></a>Valor de retorno

Uma matriz.

### <a name="example"></a>Exemplo

O seguinte exemplo mostra como usar createArray com tipos diferentes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringArray | Matriz | ["a", "b", "c"] |
| intArray | Matriz | [1, 2, 3] |
| objectArray | Matriz | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Matriz | [["one", "two", "three"]] |

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina se uma matriz, objeto ou uma cadeia de caracteres está vazio.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou cadeia de caracteres |O valor a ser verificado, caso esteja vazio. |

### <a name="return-value"></a>Valor de retorno

Retorna **True** se o valor é vazio; caso contrário, **False**.

### <a name="example"></a>Exemplo

O exemplo a seguir verifica se uma matriz, um objeto e uma cadeia de caracteres estão vazios.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True  |
| objectEmpty | Bool | True  |
| stringEmpty | Bool | True  |

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Retorna o primeiro elemento da matriz ou o primeiro caractere da cadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o primeiro elemento ou caractere. |

### <a name="return-value"></a>Valor de retorno

O tipo (cadeia de caracteres, inteiro, matriz ou objeto) do primeiro elemento em uma matriz ou o primeiro caractere de uma cadeia de caracteres.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a primeira função com uma matriz e cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Cadeia de caracteres | one |
| stringOutput | Cadeia de caracteres | O |

<a id="intersection" />

## <a name="intersection"></a>interseção
`intersection(arg1, arg2, arg3, ...)`

Retorna uma única matriz ou objeto com os elementos comuns dos parâmetros.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |objeto ou matriz |O primeiro valor a ser usado para localizar elementos comuns. |
| arg2 |Sim |objeto ou matriz |O segundo valor a ser usado para localizar elementos comuns. |
| argumentos adicionais |Não |objeto ou matriz |Os valores adicionais a serem usados para localizar elementos comuns. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou objeto com os elementos comuns.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a interseção com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objectOutput | Objeto | {"one": "a", "three": "c"} |
| arrayOutput | Matriz | ["two", "three"] |

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Retorna o último elemento da matriz ou o último caractere da cadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o último elemento ou caractere. |

### <a name="return-value"></a>Valor de retorno

O tipo (cadeia de caracteres, inteiro, matriz ou objeto) do último elemento em uma matriz ou o último caractere de uma cadeia de caracteres.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a última função com uma matriz e cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Cadeia de caracteres | three |
| stringOutput | Cadeia de caracteres | e |

<a id="length" />

## <a name="length"></a>length
`length(arg1)`

Retorna o número de elementos em uma matriz ou os caracteres em uma cadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |A matriz a ser usada para obter o número de elementos ou a cadeia de caracteres a ser usada para obter o número de caracteres. |

### <a name="return-value"></a>Valor de retorno

Um inteiro. 

### <a name="example"></a>Exemplo

O seguinte exemplo mostra como usar length com uma matriz e cadeia de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

Essa função pode ser usada com uma matriz para especificar o número de iterações durante a criação de recursos. No exemplo a seguir, o parâmetro **siteNames** faz referência a uma matriz de nomes a serem usados durante a criação de sites da web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Para saber mais sobre como usar essa função com uma matriz, confira [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

Retorna o valor mínimo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor mínimo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor mínimo.

### <a name="example"></a>Exemplo

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Retorna o valor máximo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor máximo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor máximo.

### <a name="example"></a>Exemplo

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

Cria uma matriz de inteiros a partir de um inteiro inicial e contendo um número de itens.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| startingInteger |Sim |int |O primeiro inteiro na matriz. |
| numberofElements |Sim |int |O número de inteiros na matriz. |

### <a name="return-value"></a>Valor de retorno

Uma matriz de inteiros.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a função range:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| rangeOutput | Matriz | [5, 6, 7] |

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Retorna uma matriz com todos os elementos após o número especificado na matriz, ou retorna uma cadeia de caracteres com todos os caracteres após o número especificado na cadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou cadeia de caracteres a ser usada para ignorar. |
| numberToSkip |Sim |int |O número de elementos ou caracteres a ser ignorado. Se esse valor for 0 ou menos, todos os elementos ou caracteres no valor serão retornados. Se for maior que o tamanho da matriz ou cadeia de caracteres, uma matriz ou cadeia de caracteres vazia será retornada. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="example"></a>Exemplo

O exemplo a seguir ignora o número especificado de elementos na matriz e o número especificado de caracteres em uma cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["three"] |
| stringOutput | Cadeia de caracteres | two three |

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Retorna uma matriz com o número especificado de elementos desde o início da matriz, ou uma cadeia de caracteres com o número especificado de caracteres desde o início da cadeia de caracteres.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou cadeia de caracteres da qual extrair os elementos. |
| numberToTake |Sim |int |O número de elementos ou caracteres a ser extraído. Se esse valor for 0 ou menos, uma matriz ou cadeia de caracteres vazia será retornada. Se for maior que o tamanho da matriz ou cadeia de caracteres especificada, todos os elementos da matriz ou cadeia de caracteres serão retornados. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="example"></a>Exemplo

O exemplo a seguir extrai o número especificado de elementos da matriz e de caracteres de uma cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["one", "two"] |
| stringOutput | Cadeia de caracteres | em |

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

Retorna uma única matriz ou objeto com todos os elementos dos parâmetros. Valores duplicados ou chaves só são incluídos uma vez.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |objeto ou matriz |O primeiro valor a ser usado para unir elementos. |
| arg2 |Sim |objeto ou matriz |O segundo valor a ser usado para unir elementos. |
| argumentos adicionais |Não |objeto ou matriz |Valores adicionais a serem usados para unir elementos. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou objeto.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a union com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objectOutput | Objeto | {"one": "a", "two": "b", "three": "c", "four": "d", "five": "e"} |
| arrayOutput | Matriz | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md).


