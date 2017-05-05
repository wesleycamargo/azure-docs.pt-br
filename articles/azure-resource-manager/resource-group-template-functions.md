---
title: "Funções do modelo do Resource Manager | Microsoft Docs"
description: "Descreve as funções a serem usadas no modelo do Gerenciador de Recursos do Azure para recuperar valores, trabalhar com cadeias de caracteres e numéricos e recuperar informações de implantação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 591749e2a91f8dcc080b5fa697c1f9bf953d836f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-resource-manager-template-functions"></a>Funções do modelo do Gerenciador de Recursos do Azure
Este tópico descreve todas as funções que você pode usar em um modelo do Azure Resource Manager.

As funções do modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma forma. Quando avaliada, a função preservará as maiúsculas e minúsculas, a menos que a função modifique-as expressamente (como toUpper ou toLower). Determinados tipos de recursos podem ter requisitos de maiúsculas e minúsculas independentemente de como as funções são avaliadas.

## <a name="numeric-functions"></a>Funções numéricas
O Gerenciador de Recursos fornece as seguintes funções para trabalhar com números inteiros:

* [adicionar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [int](#int)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

### <a name="add"></a>Adicionar
`add(operand1, operand2)`

Retorna a soma dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- | 
|operand1 |Sim |Número inteiro |Primeiro número a ser adicionado. |
|operand2 |Sim |Número inteiro |Segundo número a ser adicionado. |

O exemplo a seguir adiciona dois parâmetros.

```json
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
...
"outputs": {
  "addResult": {
    "type": "int",
    "value": "[add(parameters('first'), parameters('second'))]"
  }
}
```

<a id="copyindex" />

### <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Retorna o índice de um loop de iteração. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| deslocamento |Não |Número inteiro |O número a ser adicionado ao valor de iteração com base em zero. |

Essa função é sempre usada com um objeto **copy** . Se nenhum valor for fornecido para **offset**, o valor de iteração atual retornará. O valor de iteração começa em zero. Para obter uma descrição completa de como usar **copyIndex**, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

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

<a id="div" />

### <a name="div"></a>div
`div(operand1, operand2)`

Retorna a divisão de inteiros dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Número inteiro |O número que está sendo dividido. |
| operand2 |Sim |Número inteiro |O número usado para dividir. Não pode ser 0. |

O exemplo a seguir divide um parâmetro por outro parâmetro.

```json
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
...
"outputs": {
  "divResult": {
    "type": "int",
    "value": "[div(parameters('first'), parameters('second'))]"
  }
}
```

<a id="int" />

### <a name="int"></a>int
`int(valueToConvert)`

Converte o valor especificado em um inteiro.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |Cadeia de caracteres ou número inteiro |O valor a ser convertido em um inteiro. |

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em Integer.

```json
"parameters": {
    "appId": { "type": "string" }
},
"variables": { 
    "intValue": "[int(parameters('appId'))]"
}
```

<a id="mod" />

### <a name="mod"></a>mod
`mod(operand1, operand2)`

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Número inteiro |O número que está sendo dividido. |
| operand2 |Sim |Número inteiro |O número usado para dividir. Não pode ser 0. |

O exemplo a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

```json
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
...
"outputs": {
  "modResult": {
    "type": "int",
    "value": "[mod(parameters('first'), parameters('second'))]"
  }
}
```

<a id="mul" />

### <a name="mul"></a>mul
`mul(operand1, operand2)`

Retorna a multiplicação de dois inteiros fornecidos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Número inteiro |Primeiro número a ser multiplicado. |
| operand2 |Sim |Número inteiro |Segundo número a ser multiplicado. |

O exemplo a seguir multiplica um parâmetro por outro parâmetro.

```json
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
...
"outputs": {
  "mulResult": {
    "type": "int",
    "value": "[mul(parameters('first'), parameters('second'))]"
  }
}
```

<a id="sub" />

### <a name="sub"></a>sub
`sub(operand1, operand2)`

Retorna a subtração dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |Número inteiro |O número do qual é subtraído. |
| operand2 |Sim |Número inteiro |O número subtraído. |

O exemplo a seguir subtrai um parâmetro de outro parâmetro.

```json
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
...
"outputs": {
  "subResult": {
    "type": "int",
    "value": "[sub(parameters('first'), parameters('second'))]"
  }
}
```

## <a name="string-functions"></a>Funções de cadeia de caracteres
O Gerenciador de Recursos fornece as seguintes funções para trabalhar com cadeias de caracteres:

* [base64](#base64)
* [concat](#concat)
* [length](#lengthstring)
* [padLeft](#padleft)
* [substitui](#replace)
* [skip](#skipstring)
* [split](#split)
* [string](#string)
* [substring](#substring)
* [take](#takestring)
* [toLower](#tolower)
* [toUpper](#toupper)
* [cortar](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)

<a id="base64" />

### <a name="base64"></a>base64
`base64 (inputString)`

Retorna a representação base64 da cadeia de caracteres de entrada.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |Cadeia de caracteres |O valor a retornar como uma representação base64. |

O exemplo a seguir mostra como usar a função base64.

```json
"variables": {
  "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
  "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

<a id="concat" />

### <a name="concat---string"></a>concat - cadeia de caracteres
`concat (string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna o resultado concatenado. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Sim |string |O primeiro valor de concatenação. |
| cadeias de caracteres adicionais |Não |Cadeia de caracteres |Valores adicionais em ordem sequencial para concatenação. |

Essa função pode conter qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes como parâmetros. Para obter um exemplo de concatenação de matrizes, confira [concat - matriz](#concatarray).

O exemplo a seguir mostra como combinar diversos valores de cadeia de caracteres para retornar uma cadeia de caracteres concatenada.

```json
"outputs": {
    "siteUri": {
      "type": "string",
      "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

<a id="lengthstring" />

### <a name="length---string"></a>lenght - cadeia de caracteres
`length(string)`

Retorna o número de caracteres em uma cadeia de caracteres.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| string |Sim |Cadeia de caracteres |O valor a ser usado para obter o número de caracteres. |

Para obter um exemplo de como usar lenght com uma matriz, confira [lenght - matriz](#length).

O exemplo a seguir retorna o número de caracteres em uma cadeia de caracteres. 

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "nameLength": "[length(parameters('appName'))]"
}
```

<a id="padleft" />

### <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Retorna uma cadeia de caracteres alinhada à direita adicionando caracteres à esquerda até alcançar o comprimento total especificado.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToPad |Sim |Cadeia de caracteres ou número inteiro |O valor para alinhar à direita. |
| totalLength |Sim |Número inteiro |O número total de caracteres na cadeia de caracteres retornada. |
| paddingCharacter |Não |Caractere único |O caractere a ser usado para o preenchimento à esquerda até que o tamanho total seja atingido. O valor padrão é um espaço. |

O exemplo a seguir mostra como preencher o valor do parâmetro fornecido pelo usuário adicionando o caractere zero até que a cadeia de caracteres atinja 10 caracteres. Se o valor do parâmetro original for maior que 10 caracteres, nenhum caractere será adicionado.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
}
```

<a id="replace" />

### <a name="replace"></a>substitui
`replace(originalString, oldCharacter, newCharacter)`

Retorna uma nova cadeia de caracteres com todas as instâncias de um caractere na cadeia de caracteres especificada substituída por outro caractere.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalString |Sim |Cadeia de caracteres |O valor no qual todas as instâncias de um caractere são substituídas por outro caractere. |
| oldCharacter |Sim |Cadeia de caracteres |O caractere a ser removido da cadeia de caracteres original. |
| newCharacter |Sim |Cadeia de caracteres |O caractere a ser adicionado no lugar do caractere removido. |

O exemplo a seguir mostra como remover todos os traços da cadeia de caracteres fornecida pelo usuário.

```json
"parameters": {
    "identifier": { "type": "string" }
},
"variables": { 
    "newidentifier": "[replace(parameters('identifier'),'-','')]"
}
```

<a id="skipstring" />

### <a name="skip---string"></a>skip - cadeia de caracteres
`skip(originalValue, numberToSkip)`

Retorna uma cadeia de caracteres com todos os caracteres após o número especificado na cadeia de caracteres.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |string |A cadeia de caracteres a ser ignorada. |
| numberToSkip |Sim |Número inteiro |O número de caracteres a serem ignorados. Se esse valor for 0 ou menor, todos os caracteres na cadeia de caracteres retornarão. Se for maior do que o tamanho da cadeia de caracteres, uma cadeia de caracteres vazia retornará. |

Para obter um exemplo de como usar skip com uma matriz, confira [skip - matriz](#skip).

O exemplo a seguir ignora o número especificado de caracteres na cadeia de caracteres.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for skipping"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[skip(parameters('first'),parameters('second'))]"
  }
}
```

<a id="split" />

### <a name="split"></a>split
`split(inputString, delimiterString)`

`split(inputString, delimiterArray)`

Retorna uma matriz de cadeias de caracteres que contém as subcadeias de caracteres da cadeia de caracteres de entrada que são delimitadas por delimitadores especificados.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |Cadeia de caracteres |A cadeia de caracteres a dividir. |
| delimiter |Sim |Cadeia de caracteres ou Matriz de cadeias de caracteres |O delimitador a ser usado para dividir a cadeia de caracteres. |

O exemplo a seguir divide a cadeia de caracteres de entrada com uma vírgula.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "stringPieces": "[split(parameters('inputString'), ',')]"
}
```

O exemplo a seguir divide a cadeia de caracteres de entrada com uma vírgula ou ponto e vírgula.

```json
"variables": {
  "stringToSplit": "test1,test2;test3",
  "delimiters": [ ",", ";" ]
},
"resources": [ ],
"outputs": {
  "exampleOutput": {
    "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
    "type": "array"
  }
}
```

<a id="string" />

### <a name="string"></a>string
`string(valueToConvert)`

Converte o valor especificado em uma cadeia de caracteres.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim | Qualquer |O valor a ser convertido em cadeia de caracteres. Qualquer tipo de valor pode ser convertido, incluindo objetos e matrizes. |

O exemplo a seguir converte os valores de parâmetro fornecidos pelo usuário em cadeias de caracteres.

```json
"parameters": {
  "jsonObject": {
    "type": "object",
    "defaultValue": {
      "valueA": 10,
      "valueB": "Example Text"
    }
  },
  "jsonArray": {
    "type": "array",
    "defaultValue": [ "a", "b", "c" ]
  },
  "jsonInt": {
    "type": "int",
    "defaultValue": 5
  }
},
"variables": { 
  "objectString": "[string(parameters('jsonObject'))]",
  "arrayString": "[string(parameters('jsonArray'))]",
  "intString": "[string(parameters('jsonInt'))]"
}
```

<a id="substring" />

### <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Retorna uma subcadeia de caraceteres que começa na posição do caractere especificado e contém o número especificado de caracteres.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToParse |Sim |string |A cadeia original da qual a subcadeia de caracteres é extraída. |
| startIndex |Não |Número inteiro |A posição inicial do caractere baseada em zero para a subcadeia de caracteres. |
| length |Não |Número inteiro |O número de caracteres para a subcadeia de caracteres. Deve se referir a uma localização dentro da cadeia de caracteres. |

O exemplo a seguir extrai os três primeiros caracteres de um parâmetro.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 3)]"
}
```

O exemplo a seguir falhará com o erro “Os parâmetros de índice e de comprimento devem se referir a uma localização dentro da cadeia de caracteres. O parâmetro de índice: '0', o parâmetro de comprimento: '11', o comprimento do parâmetro de cadeia de caracteres: '10'”.

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

<a id="takestring" />

### <a name="take---string"></a>take - cadeia de caracteres
`take(originalValue, numberToTake)`

Retorna uma cadeia de caracteres com o número especificado de caracteres no início da cadeia.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |Cadeia de caracteres |O valor do qual retirar os caracteres. |
| numberToTake |Sim |Número inteiro |O número de caracteres a serem retirados. Se esse valor for 0 ou menos, uma cadeia de caracteres vazia retornará. Se for maior do que o tamanho da cadeia de caracteres especificada, todos os caracteres da cadeia retornarão. |

Para obter um exemplo de como usar take com uma matriz, confira [take - matriz](#take).

O exemplo a seguir retira o número especificado de caracteres da cadeia de caracteres.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for taking"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[take(parameters('first'), parameters('second'))]"
  }
}
```

<a id="tolower" />

### <a name="tolower"></a>toLower
`toLower(stringToChange)`

Converte a cadeia de caracteres especificada em letras minúsculas.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |Cadeia de caracteres |O valor a ser convertido em letras minúsculas. |

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em letras minúsculas.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "lowerCaseAppName": "[toLower(parameters('appName'))]"
}
```

<a id="toupper" />

### <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Converte a cadeia de caracteres especificada em maiúsculas.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |Cadeia de caracteres |O valor a ser convertido em letras maiúsculas. |

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em letras maiúsculas.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "upperCaseAppName": "[toUpper(parameters('appName'))]"
}
```

<a id="trim" />

### <a name="trim"></a>cortar
`trim (stringToTrim)`

Remove todos os caracteres de espaço em branco à esquerda e à direita da cadeia de caracteres especificada.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sim |Cadeia de caracteres |O valor de corte. |

O exemplo a seguir remove os caracteres de espaço em branco do valor de parâmetro fornecido pelo usuário.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "trimAppName": "[trim(parameters('appName'))]"
}
```

<a id="uniquestring" />

### <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Cria uma cadeia de caracteres de hash determinístico com base nos valores fornecidos como parâmetros. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseString |Sim |string |O valor usado na função de hash para criar uma cadeia de caracteres exclusiva. |
| parâmetros extras conforme necessário |Não |Cadeia de caracteres |Você pode adicionar quantas cadeias de caracteres forem necessárias para criar o valor que especifica o nível de exclusividade. |

Essa função é útil quando você precisa criar um nome exclusivo para um recurso. Você fornece valores de parâmetros que limitam o escopo de exclusividade para o resultado. Você pode especificar se o nome é exclusivo para a assinatura, grupo de recursos ou implantação. 

O valor retornado não é uma cadeia de caracteres aleatória, mas sim o resultado de uma função de hash. O valor retornado tem 13 caracteres. Não é globalmente exclusivo. Você talvez queira combinar o valor com um prefixo de sua convenção de nomenclatura para criar um nome significativo. O exemplo a seguir mostra o formato do valor retornado. O valor real poderá variar de acordo com os parâmetros fornecidos.

    tcvhiyu5h2o5o

Os exemplos a seguir mostram como usar uniqueString para criar um valor exclusivo para níveis usados com mais frequência.

Escopo exclusivo para a assinatura

```json
"[uniqueString(subscription().subscriptionId)]"
```

Escopo exclusivo para o grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Escopo exclusivo para a implantação de um grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

O exemplo a seguir mostra como criar um nome exclusivo para uma conta de armazenamento com base em seu grupo de recursos. Dentro do grupo de recursos, o nome não é exclusivo se for construído da mesma maneira.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```


<a id="uri" />

### <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

Cria um URI absoluto, combinando o baseUri e a cadeia de caracteres relativeUri.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseUri |Sim |Cadeia de caracteres |Cadeia de caracteres do URI de base. |
| relativeUri |Sim |string |Cadeia de caracteres de uri relativo para adicionar a cadeia de caracteres do uri de base. |

O valor para o parâmetro **baseUri** pode incluir um arquivo específico, mas apenas o caminho base é usado ao construir a URI. Por exemplo, transmitir `http://contoso.com/resources/azuredeploy.json` como parâmetro baseUri resultará em uma URI base de `http://contoso.com/resources/`.

O exemplo a seguir mostra como criar um link para um modelo aninhado com base no valor do modelo pai.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

## <a name="array-functions"></a>Funções de matriz
O Gerenciador de Recursos fornece diversas funções para trabalhar com valores de matriz.

* [concat](#concatarray)
* [length](#length)
* [skip](#skip)
* [take](#take)

Para obter uma matriz de valores de cadeia de caracteres delimitada por um valor, confira [split](#split).

<a id="concatarray" />

### <a name="concat---array"></a>concat - matriz
`concat (array1, array2, array3, ...)`

Combina várias matrizes e retorna a matriz concatenada. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| array1 |Sim |Matriz |A primeira matriz de concatenação. |
| matrizes adicionais |Não |Matriz |Matrizes adicionais em ordem sequencial para concatenação. |

Essa função pode conter qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes como parâmetros. Para obter um exemplo de concatenação de valores de cadeia de caracteres, confira [concat - cadeia de caracteres](#concat).

O próximo exemplo mostra como combinar duas matrizes.

```json
"parameters": {
    "firstarray": {
      "type": "array"
    }
    "secondarray": {
      "type": "array"
    }
},
"variables": {
    "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]"
}
```

<a id="length" />

### <a name="length---array"></a>lenght - matriz
`length(array)`

Retorna o número de elementos em uma matriz.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| array |Sim |Matriz |A matriz a ser usada para obter o número de elementos. |

Essa função pode ser usada com uma matriz para especificar o número de iterações durante a criação de recursos. No exemplo a seguir, o parâmetro **siteNames** faz referência a uma matriz de nomes a serem usados durante a criação de sites da web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Para saber mais sobre como usar essa função com uma matriz, confira [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md). 

Para obter um exemplo de como usar lenght com um valor de cadeia de caracteres, confira [lenght - cadeia de caracteres](#lengthstring).

<a id="skip" />

### <a name="skip---array"></a>skip - matriz
`skip(originalValue, numberToSkip)`

Retorna uma matriz com todos os elementos após o número especificado na matriz.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |Matriz |A matriz a ser ignorada. |
| numberToSkip |Sim |Número inteiro |O número de elementos a serem ignorados. Se esse valor for 0 ou menor, todos os elementos da matriz retornarão. Se for maior do que o tamanho da matriz, uma matriz vazia retornará. |

Para obter um exemplo de como usar skip com uma cadeia de caracteres, confira [skip - cadeia de caracteres](#skipstring).

O exemplo a seguir ignora o número especificado de elementos na matriz.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for skipping"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[skip(parameters('first'), parameters('second'))]"
  }
}
```

<a id="take" />

### <a name="take---array"></a>take - matriz
`take(originalValue, numberToTake)`

Retorna uma matriz com o número especificado de elementos desde o início da matriz.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |Matriz |A matriz da qual retirar os elementos. |
| numberToTake |Sim |Número inteiro |O número de elementos a serem retirados. Se esse valor for 0 ou menor, uma cadeia de caracteres vazia retornará. Se for maior do que o tamanho da matriz especificada, todos os elementos da matriz retornarão. |

Para obter um exemplo de como usar take com uma cadeia de caracteres, confira [take - cadeia de caracteres](#takestring).

O exemplo a seguir usa o número especificado de elementos da matriz.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for taking"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[take(parameters('first'),parameters('second'))]"
  }
}
```

## <a name="deployment-value-functions"></a>Funções de valor de implantação
O Gerenciador de Recursos fornece as seguintes funções para obter os valores de seções do modelo e os valores relacionados à implantação:

* [implantação](#deployment)
* [parâmetros](#parameters)
* [variáveis](#variables)

Para obter valores de recursos, de grupos de recursos ou de assinaturas, veja [Funções de recurso](#resource-functions).

<a id="deployment" />

### <a name="deployment"></a>implantação
`deployment()`

Retorna informações sobre a operação de implantação atual.

Essa função retorna o objeto que é passado durante a implantação. As propriedades no objeto retornado vão variar dependendo se o objeto de implantação for transmitido como um link ou como um objeto na linha. 

Quando o objeto de implantação é passado na linha, como ao usar o parâmetro **-TemplateFile** no Azure PowerShell para apontar para um arquivo local, o objeto retornado tem no seguinte formato:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando o objeto é transmitido como um link, como ao usar o parâmetro **- TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

O exemplo a seguir mostra como usar a implantação() para um modelo aninhado com base no URI do modelo pai.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

<a id="parameters" />

### <a name="parameters"></a>parâmetros
`parameters (parameterName)`

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |Cadeia de caracteres |O nome do parâmetro a retornar. |

O exemplo a seguir mostra um uso simplificado da função parâmetros.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

<a id="variables" />

### <a name="variables"></a>variáveis
`variables (variableName)`

Retorna o valor da variável. O nome do parâmetro especificado deve ser definido na seção variáveis do modelo.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |Cadeia de caracteres |O nome da variável a retornar. |

O exemplo a seguir usa um valor variável.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

## <a name="resource-functions"></a>Funções de recurso
O Gerenciador de Recursos fornece as seguintes funções para obter valores de recurso:

* [listKeys e list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

Para obter valores de parâmetros, de variáveis ou da implantação atual, veja [Funções de valor de implantação](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />

### <a name="listkeys-and-listvalue"></a>listKeys e list{Value}
`listKeys (resourceName or resourceIdentifier, apiVersion)`

`list{Value} (resourceName or resourceIdentifier, apiVersion)`

Retorna os valores para qualquer tipo de recurso que ofereça suporte à operação de lista. O uso mais comum é **listKeys**. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |Cadeia de caracteres |Identificador exclusivo para o recurso. |
| apiVersion |Sim |Cadeia de caracteres |Versão de API do estado de tempo de execução do recurso. Normalmente, no formato **aaaa-mm-dd**. |

Qualquer operação que começa com **list** pode ser usada como uma função no seu modelo. As operações disponíveis não incluem apenas **listKeys**, mas também operações como **list**, **listAdminKeys** e **listStatus**. Para determinar quais tipos de recursos têm uma operação de lista, consulte as [operações da API REST](/rest/api/) do provedor de recursos.

Para encontrar as operações de lista de um provedor de recursos, use o seguinte cmdlet do PowerShell:

```powershell
Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
```

Para encontrar as operações de lista de um provedor de recursos, use o seguinte comando da CLI do Azure e o utilitário JSON [jq](http://stedolan.github.io/jq/download/) para filtrar apenas as operações de lista:

```azurecli
azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
```

A resourceId pode ser especificada usando a [função resourceId](#resourceid) ou o formato **{providerNamespace}/{resourceType}/{resourceName}**.

O exemplo a seguir mostra como retornar as chaves primárias e secundárias de uma conta de armazenamento na seção de saídas.

```json
"outputs": { 
  "listKeysOutput": { 
    "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
    "type" : "object" 
  } 
}
``` 

O objeto retornado de listKeys tem o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

<a id="providers" />

### <a name="providers"></a>providers
`providers (providerNamespace, [resourceType])`

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se você não fornecer um tipo de recurso, a função retornará todos os tipos com suporte para o provedor de recursos.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sim |Cadeia de caracteres |Namespace do provedor |
| resourceType |Não |string |O tipo de recurso no namespace especificado. |

Cada tipo com suporte é retornado no seguinte formato. A ordenação de matrizes não é garantida.

```json
{
    "resourceType": "",
    "locations": [ ],
    "apiVersions": [ ]
}
```

O exemplo a seguir mostra como usar a função provider:

```json
"outputs": {
    "exampleOutput": {
        "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
        "type" : "object"
    }
}
```

<a id="reference" />

### <a name="reference"></a>reference
`reference (resourceName or resourceIdentifier, [apiVersion])`

Retorna um objeto que representa o estado de tempo de execução de outro recurso.

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |Cadeia de caracteres |Nome ou identificador exclusivo de um recurso. |
| apiVersion |Não |Cadeia de caracteres |Versão da API do recurso especificado. Inclua esse parâmetro quando o recurso não estiver provisionado no mesmo modelo. Normalmente, no formato **aaaa-mm-dd**. |

A função **referência** deriva seu valor de um estado de tempo de execução e, portanto, não pode ser usada na seção de variáveis. Ela pode ser usada na seção de saídas de um modelo.

Usando a função de referência, você declara implicitamente que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo. Você não precisa usar a propriedade **dependsOn** também. A função não é avaliada até que o recurso referenciado conclua a implantação.

O exemplo a seguir faz referência a uma conta de armazenamento implantada no mesmo modelo.

```json
"outputs": {
    "NewStorage": {
        "value": "[reference(parameters('storageAccountName'))]",
        "type" : "object"
    }
}
```

O exemplo a seguir faz referência a uma conta de armazenamento que não foi implantada no modelo, mas existe dentro do mesmo grupo de recursos que os recursos que estão sendo implantados.

```json
"outputs": {
    "ExistingStorage": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
        "type" : "object"
    }
}
```

Você pode recuperar um valor específico do objeto retornado, como o URI do ponto de extremidade do blob, conforme mostra o exemplo a seguir:

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

O exemplo a seguir faz referência a uma conta de armazenamento em um grupo de recursos diferente.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

As propriedades no objeto retornado da função de **referência** variam por tipo de recurso. Para ver os valores e nomes de propriedade para um tipo de recurso, crie um modelo simples que retorne o objeto na seção de **saídas** . Se você tiver um recurso existente desse tipo, o modelo retornará apenas o objeto sem implantar qualquer recurso. Se você não tiver um recurso existente desse tipo, o modelo implantará apenas esse tipo e retornará o objeto. Em seguida, adicione essas propriedades a outros modelos que precisam recuperar dinamicamente os valores durante a implantação. 

<a id="resourcegroup" />

### <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Retorna um objeto que representa o grupo de recursos atual. 

O objeto retornado está no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

O exemplo a seguir usa o local do grupo de recursos para atribuir o local de um site Web.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

<a id="resourceid" />

### <a name="resourceid"></a>resourceId
`resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Retorna o identificador exclusivo de um recurso. 

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |Cadeia de caracteres (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. |
| resourceGroupName |Não |Cadeia de caracteres |O valor padrão é o grupo de recursos atual. Especifique esse valor quando você precisar recuperar um recurso em outro grupo de recursos. |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |Cadeia de caracteres |Nome do recurso. |
| resourceName2 |Não |Cadeia de caracteres |Próximo segmento de nome do recurso se o recurso está aninhado. |

Você pode usar essa função quando o nome do recurso é ambíguo ou não provisionado no mesmo modelo. O identificador é retornado no seguinte formato:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

O exemplo a seguir mostra como recuperar as IDs de recurso para um site Web e um banco de dados. O site Web existe em um grupo de recursos denominado **myWebsitesGroup** e o banco de dados existe no grupo de recursos para este modelo.

```json
[resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

Frequentemente, você precisa usar essa função ao usar uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. A conta de armazenamento ou a rede virtual pode ser usada em vários grupos de recursos; portanto, você não deve excluí-los ao excluir um único grupo de recursos. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

<a id="subscription" />

### <a name="subscription"></a>subscription
`subscription()`

Retorna detalhes sobre a assinatura no formato a seguir:

```json
{
    "id": "/subscriptions/#####",
    "subscriptionId": "#####",
    "tenantId": "#####"
}
```

O exemplo a seguir mostra a função de assinatura chamada na seção de saídas. 

```json
"outputs": { 
  "exampleOutput": { 
      "value": "[subscription()]", 
      "type" : "object" 
  } 
} 
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Gerenciador de Recursos do Azure, veja a seção [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md)
* Para mesclar diversos modelos, confira a seção [Como usar modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md)
* Para iterar um número de vezes especificado ao criar um tipo de recurso, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md)
* Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md)


