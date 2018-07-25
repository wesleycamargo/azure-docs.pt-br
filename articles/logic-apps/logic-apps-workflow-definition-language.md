---
title: Esquema de linguagem de definição de fluxo de trabalho - Aplicativo Lógico do Azure | Microsoft Docs
description: Escreva definições de fluxo de trabalho personalizadas para os Aplicativos Lógicos do Azure usando a Linguagem de Definição de Fluxo de Trabalho
services: logic-apps
author: ecfan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: babe21db6acc2f7154857b4eb0a02356e89a8ca7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059228"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Definições de fluxo de trabalho dos Aplicativos Lógicos com o esquema de Linguagem de Definição de Fluxo de Trabalho

Quando você cria um fluxo de trabalho de aplicativo lógico com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), a definição subjacente do fluxo de trabalho descreve a lógica real executada no aplicativo lógico. Essa descrição segue uma estrutura definida e validada pelo esquema de Linguagem de Definição de Fluxo de Trabalho, que usa o formato [JSON (JavaScript Object Notation)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Estrutura da definição de fluxo de trabalho

Uma definição de fluxo de trabalho tem pelo menos um gatilho que cria uma instância do aplicativo lógico, além de uma ou mais ações executadas pelo aplicativo lógico. 

Esta é a estrutura de alto nível de uma definição de fluxo de trabalho:  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Elemento | Obrigatório | DESCRIÇÃO | 
|---------|----------|-------------| 
| definição | SIM | O elemento inicial da definição de fluxo de trabalho | 
| $schema | Somente ao referenciar uma definição de fluxo de trabalho externamente | O local do arquivo de esquema JSON que descreve a versão da Linguagem de Definição de Fluxo de Trabalho, que pode ser encontrado aqui: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Não  | O número de versão da definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. | 
| parâmetros | Não  | As definições de um ou mais parâmetros que passam dados para o fluxo de trabalho <p><p>Máximo de parâmetros: 50 | 
| gatilhos | Não  | As definições de um ou mais gatilhos que criam uma instância do fluxo de trabalho. É possível definir mais de um gatilho, mas apenas com a Linguagem de Definição de Fluxo de Trabalho, e não visualmente por meio do Designer de Aplicativos Lógicos. <p><p>Máximo da gatilhos: 10 | 
| Ações | Não  | As definições de uma ou mais ações a serem executadas no tempo de execução do fluxo de trabalho <p><p>Máximo de ações: 250 | 
| outputs | Não  | As definições das saídas retornadas de uma execução do fluxo de trabalho <p><p>Máximo de saídas: 10 |  
|||| 

## <a name="parameters"></a>parâmetros

Na seção `parameters`, defina todos os parâmetros de fluxo de trabalho usados por seu aplicativo lógico na implantação para aceitar entradas. Declarações de parâmetro e valores de parâmetros são necessários na implantação. Antes de usar esses parâmetros em outras seções do fluxo de trabalho, não deixe de declarar todos os parâmetros nessas seções. 

Esta é a estrutura geral de uma definição de parâmetro:  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| Elemento | Obrigatório | Tipo | DESCRIÇÃO |  
|---------|----------|------|-------------|  
| Tipo | SIM | int, float, string, securestring, bool, array, objeto JSON, secureobject <p><p>**Observação**: para todas as senhas, chaves e segredos, use os tipos `securestring` e `secureobject`, porque a operação `GET` não retorna esses tipos. | O tipo do parâmetro |
| defaultValue | Não  | O mesmo que `type` | O valor de parâmetro padrão quando nenhum valor é especificado ao criar uma instância do fluxo de trabalho | 
| allowedValues | Não  | O mesmo que `type` | Uma matriz com valores que o parâmetro pode aceitar |  
| metadata | Não  | Objeto JSON | Qualquer outro detalhe do parâmetro, por exemplo, o nome ou uma descrição legível do aplicativo lógico ou dados de tempo de design usados pelo Visual Studio ou por outras ferramentas |  
||||

## <a name="triggers-and-actions"></a>Gatilhos e ações  

Em uma definição de fluxo de trabalho, as seções `triggers` e `actions` definem as chamadas que ocorrem durante a execução do fluxo de trabalho. Para conhecer a sintaxe e obter mais informações sobre essas seções, confira [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>outputs 

Na seção `outputs`, defina os dados que o fluxo de trabalho pode retornar quando terminar sua execução. Por exemplo, para rastrear um valor ou status específico em cada execução, especifique que a saída do fluxo de trabalho retorne esses dados. 

> [!NOTE]
> Ao responder a solicitações de entrada da API REST de um serviço, não use `outputs`. Em vez disso, use o tipo de ação `Response`. Para obter mais informações, consulte [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

Esta é a estrutura geral de uma definição de saída: 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| Elemento | Obrigatório | Tipo | DESCRIÇÃO | 
|---------|----------|------|-------------| 
| <*key-name*> | SIM | Cadeia de caracteres | O nome da chave do valor retornado da saída |  
| Tipo | SIM | int, float, string, securestring, bool, array, objeto JSON | O tipo do valor retornado da saída | 
| value | SIM | O mesmo que `type` | O valor retornado da saída |  
||||| 

Para obter a saída da execução de um fluxo de trabalho, examine os detalhes e o histórico de execuções do aplicativo lógico no portal do Azure ou use a [API REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, como o Power BI, para criar dashboards. 

<a name="expressions"></a>

## <a name="expressions"></a>Expressões

Com JSON, é possível ter valores literais existentes no tempo de design, por exemplo:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Também é possível ter valores que não existem até o tempo de execução. Para representar esses valores, é possível usar *expressões*, que são avaliadas em tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções](#functions), [operadores](#operators), constantes, valores explícitos ou variáveis. Na definição de fluxo de trabalho, é possível usar uma expressão em qualquer lugar em um valor de cadeia de caracteres JSON, prefixando a expressão com o sinal de arroba (\@). Ao avaliar uma expressão que representa um valor JSON, o corpo da expressão é extraído removendo o caractere \@ e sempre resultará em outro valor JSON. 

Por exemplo, para a propriedade `customerName` definida anteriormente, você pode obter o valor da propriedade usando a função [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão de função e pode atribuir esse valor à propriedade `accountName`:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

A *interpolação de cadeia de caracteres* também permite usar várias expressões dentro de cadeias de caracteres que são encapsuladas pelo caractere \@ e por chaves ({}). Esta é a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado sempre é uma cadeia de caracteres, tornando essa funcionalidade semelhante à função `concat()`, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se você tiver uma cadeia de caracteres literal que inicia com o caractere \@, prefixe o caractere \@ com outro caractere \@ como um caractere de escape: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Result |
|------------|--------| 
| "Sophia Owen" | Retornar estes caracteres: 'Sophia Owen' |
| "array[1]" | Retornar estes caracteres: 'array[1]' |
| "\@\@" | Retornar esses caracteres como uma cadeia de caracteres com um caractere: '\@' |   
| " \@" | Retornar esses caracteres como uma cadeia de caracteres com dois caracteres: ' \@' |
|||

Para esses exemplos, suponha que você defina "myBirthMonth" como "January" e "myAge" igual ao número 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as expressões a seguir são avaliadas:

| Expressão JSON | Result |
|-----------------|--------| 
| "\@parameters('myBirthMonth')" | Retornar esta cadeia de caracteres: "January" |  
| "\@{parameters('myBirthMonth')}" | Retornar esta cadeia de caracteres: "January" |  
| "\@parameters('myAge')" | Retornar este número: 42 |  
| "\@{parameters('myAge')}" | Retornar este número como uma cadeia de caracteres: "42" |  
| "My age is \@{parameters('myAge')}" | Retornar esta cadeia de caracteres: My age is 42" |  
| "\@concat('My age is ', string(parameters('myAge')))" | Retornar esta cadeia de caracteres: My age is 42" |  
| "My age is \@\@{parameters('myAge')}" | Retornar essa cadeia de caracteres, que inclui a expressão: "My age is \@{parameters('myAge')}` | 
||| 

Quando está trabalhando visualmente no Designer de Aplicativos Lógicos, você pode criar expressões usando o Construtor de Expressões, por exemplo: 

![Designer de Aplicativos Lógicos > Construtor de Expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando você terminar, a expressão será exibida para a propriedade correspondente em sua definição de fluxo de trabalho, por exemplo, a propriedade `searchQuery` aqui:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operadores

No caso de [expressões](#expressions) e [funções](#functions), os operadores desempenham tarefas específicas, como referenciar uma propriedade ou um valor em uma matriz. 

| Operador | Tarefa | 
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, encapsule a cadeia de caracteres somente com aspas simples, por exemplo, `'<myString>'`. Não use aspas duplas (""), que entram em conflito com a formatação JSON ao redor de uma expressão inteira. Por exemplo:  <p>**Sim**: length('Hello') </br>**Não**: length("Hello") <p>Quando passa matrizes ou números, você não precisa de pontuação de encapsulamento. Por exemplo:  <p>**Sim**: length([1, 2, 3]) </br>**Não**: length("[1, 2, 3]") | 
| [] | Para referenciar um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item de uma matriz: <p>`myArray[1]` | 
| . | Para referenciar uma propriedade em um objeto, use o operador de ponto. Por exemplo, para obter a propriedade `name` para um objeto JSON `customer`: <p>`"@parameters('customer').name"` | 
| ? | Para referenciar propriedades nulas em um objeto sem erro de tempo de execução, use o operador de ponto de interrogação. Por exemplo, você pode usar esta expressão para tratar as saídas nulas de um gatilho: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm seus valores de ações de tempo de execução que podem ainda não existir quando o aplicativo lógico começa a ser executado. Para referenciar ou trabalhar com esses valores em expressões, você pode usar [*funções*](../logic-apps/workflow-definition-language-functions-reference.md). Por exemplo, é possível usar funções matemáticas para fazer cálculos, como a função [add()](../logic-apps/workflow-definition-language-functions-reference.md#add), que retorna a soma de números inteiros ou floats. Para obter informações detalhadas sobre cada função, veja o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).
Ou continue aprendendo sobre as funções e seu uso geral.

Estas são apenas algumas tarefas de exemplo que podem ser executadas com funções: 

| Tarefa | Sintaxe da função | Result | 
| ---- | --------------- | -------------- | 
| Retornar uma cadeia de caracteres em letras minúsculas. | toLower('<*texto*>') <p>Por exemplo: toLower('Hello') | "hello" | 
| Retornar um GUID (identificador global exclusivo). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Este exemplo mostra como você pode obter o valor do parâmetro `customerName` e atribui-lo à propriedade `accountName` usando a função [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão:

```json
"accountName": "@parameters('customerName')"
```

Estas são algumas outras maneiras de usar funções em expressões:

| Tarefa | Sintaxe da função em uma expressão | 
| ---- | -------------------------------- | 
| Executar o trabalho com um item passando esse item para uma função. | "\@<*functionName*>(<*item*>)" | 
| 1. Obter o valor de *parameterName* usando a função `parameters()` aninhada. </br>2. Executar o trabalho com o resultado passando esse valor para *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1. Obter o resultado da função interna aninhada *functionName*. </br>2. Passar o resultado para a função externa *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1. Obter o resultado de *functionName*. </br>2. Dado que o resultado é um objeto com a propriedade *propertyName*, obter o valor dessa propriedade. | "\@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

Por exemplo, a função `concat()` pode usar dois ou mais valores de cadeia de caracteres como parâmetros. Essa função combina essas cadeias de caracteres em uma única cadeia de caracteres. Você pode transmitir literais de cadeia de caracteres, por exemplo, "Sophia" e "Owen", para obter uma cadeia de caracteres combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ou pode obter valores de cadeia de caracteres usando parâmetros. Este exemplo usa a função `parameters()` em cada parâmetro `concat()` e os parâmetros `firstName` e `lastName`. Em seguida, passe as cadeias de caracteres resultantes para a função `concat()` para obter uma cadeia de caracteres combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, os dois exemplos atribuem o resultado à propriedade `customerName`. 

Para obter informações detalhadas sobre cada função, veja o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).
Ou continue aprendendo sobre as funções com base em seu uso geral.

<a name="string-functions"></a>

### <a name="string-functions"></a>Funções de cadeia de caracteres

Para trabalhar com cadeias de caracteres, você pode usar essas funções de cadeia de caracteres e algumas [funções de coleta](#collection-functions). Funções de cadeia de caracteres só funcionam com cadeias de caracteres. 

| Função de cadeia de caracteres | Tarefa | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verificar se uma cadeia de caracteres termina com a subcadeia de caracteres especificada. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gerar um GUID (identificador global exclusivo) como uma cadeia de caracteres. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Retornar a posição inicial de uma subcadeia de caracteres. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retornar a posição final de uma subcadeia de caracteres. | 
| [substitui](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres atualizada. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retornar uma matriz com todos os caracteres de uma cadeia de caracteres e separar cada caractere com o caractere delimitador específico. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Retornar caracteres de uma cadeia de caracteres, começando na posição especificada. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retornar uma cadeia de caracteres em letras minúsculas. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retornar uma cadeia de caracteres em letras maiúsculas. | 
| [cortar](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, matrizes em geral, cadeias de caracteres e, às vezes, dicionários, você pode usar estas funções de coleção. 

| Função de coleção | Tarefa | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verificar se uma coleção tem um item específico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Retornar o primeiro item de uma coleção. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retornar uma coleção que tem *somente* os itens comuns entre as coleções especificadas. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retornar uma cadeia de caracteres que tem *todos* os itens de uma matriz, separados pelo caractere especificado. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retornar o último item de uma coleção. | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Retornar o número de itens em uma cadeia de caracteres ou matriz. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Remover itens do início de uma coleção e retornar *todos os outros* itens. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Retornar itens do início de uma coleção. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retornar uma coleção que tem *todos* os itens das coleções especificadas. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Funções de comparação

Para trabalhar com condições, comparar valores e resultados de expressões ou avaliar vários tipos de lógica, você pode usar estas funções de comparação. Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de comparação | Tarefa | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verificar se todas as expressões são verdadeiras. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verificar se os dois valores são equivalentes. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verificar se o primeiro valor é maior que o segundo valor. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verificar se o primeiro valor é superior ou igual ao segundo. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verificar se uma expressão é verdadeira ou falsa. Com base no resultado, retornar um valor especificado. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verificar se o primeiro valor é menor que o segundo valor. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verificar se o primeiro valor é inferior ou igual ao segundo valor. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verificar se uma expressão é falsa. | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verificar se pelo menos uma expressão é verdadeira. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou o formato de um valor, você pode usar estas funções de conversão. Por exemplo, é possível alterar um valor de booliano para inteiro. Para saber como os Aplicativos Lógicos tratam tipos de conteúdo durante a conversão, consulte [Handle content types](../logic-apps/logic-apps-content-type.md) (Tratar tipos de conteúdo). Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de conversão | Tarefa | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retornar uma matriz de uma única entrada especificada. Para várias entradas, confira [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retornar a versão codificada em Base64 de uma cadeia de caracteres. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada em Base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retornar a versão binária de um valor de entrada. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retornar a versão booliana de um valor de entrada. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retornar uma matriz de várias entradas. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Retornar o URI de dados de um valor de entrada. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retornar a versão binária de um URI de dados. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retornar a versão de cadeia de caracteres de um URI de dados. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retornar a versão binária de um URI de dados. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres de escape por versões decodificadas. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres não seguros para URL por caracteres de escape. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Retornar um número de ponto flutuante correspondente a um valor de entrada. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retornar a versão em inteiros de uma cadeia de caracteres. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) correspondente a uma cadeia de caracteres ou XML. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retornar a versão de cadeia de caracteres de um valor de entrada. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retornar a versão codificada para URI correspondente a um valor de entrada substituindo caracteres não seguros para URL por caracteres de escape. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada como URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retornar a versão de cadeia de caracteres correspondente a uma cadeia de caracteres codificada como URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Retornar a versão XML de uma cadeia de caracteres. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Funções matemáticas

Para trabalhar com números inteiros e floats, você pode usar estas funções matemáticas. Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função matemática | Tarefa | 
| ------------- | ---- | 
| [adicionar](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retornar o resultado da adição de dois números. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retornar o resultado da divisão de dois números. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retornar o valor mais alto de um conjunto de números em uma matriz. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retornar o valor mais baixo de um conjunto de números ou de uma matriz. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retornar o resto da divisão de dois números. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retornar o produto da multiplicação de dois números. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retornar um número inteiro aleatório de um intervalo especificado. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retornar uma matriz de inteiros que começa em um inteiro especificado. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retornar o resultado da subtração do segundo número do primeiro. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horas, você pode usar estas funções de data e hora.
Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de data ou hora | Tarefa | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicionar um número de dias a um carimbo de data/hora. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicionar um número de horas a um carimbo de data/hora. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicionar um número de minutos a um carimbo de data/hora. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicionar um número de segundos a um carimbo de data/hora. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adicionar um número de unidades de tempo a um carimbo de data/hora. Confira também [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado) no fuso horário de destino. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converter um carimbo de data/hora do fuso horário de origem no fuso horário de destino. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converter um carimbo de data/hora do fuso horário de origem em UTC (Tempo Universal Coordenado). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retornar o componente de dia do mês de um carimbo de data/hora. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retornar o componente de dia da semana de um carimbo de data/hora. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retornar o componente de dia do ano de um carimbo de data/hora. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Retornar a data de um carimbo de data/hora. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. Confira também [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. Confira também [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Retornar o início do dia de um carimbo de data/hora. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Retornar o início da hora de um carimbo de data/hora. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retornar o início do mês de um carimbo de data/hora. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrair um número de unidades de tempo de um carimbo de data/hora. Confira também [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Retornar o valor de propriedade `ticks` de um carimbo de data/hora especificado. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Retornar o carimbo de data/hora atual como uma cadeia de caracteres. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funções de fluxo de trabalho

Essas funções de fluxo de trabalho podem ajudar você a:

* Obter detalhes sobre uma instância de fluxo de trabalho em tempo de execução. 
* Trabalhar com as entradas usadas para criar uma instância de Aplicativos Lógicos.
* Referenciar as saídas de gatilhos e ações.

Por exemplo, você pode referenciar as saídas de uma ação e usar esses dados em uma ação posterior. Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de fluxo de trabalho | Tarefa | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Retornar a saída da ação atual em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira também [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Retornar a saída `body` de uma ação em tempo de execução. Confira também [body](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Retornar a saída de uma ação em tempo de execução. Confira [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Retornar a saída de uma ação em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira também [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Retornar a saída `body` de uma ação em tempo de execução. Confira também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Criar uma matriz com os valores correspondentes a um nome de chave nas saídas da ação *form-data* ou *form-encoded*. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Retornar um único valor correspondente a um nome de chave na saída *form-data* ou *form-encoded* de uma ação. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando está dentro de uma ação repetida em uma matriz, retornar o item atual na matriz durante a iteração atual da ação. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando está dentro de um for-each ou do-until-loop, retornar o item atual do loop especificado.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Retornar a "URL de retorno de chamada" que chama um gatilho ou uma ação. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Retornar o corpo de uma parte específica na saída de uma ação que tem várias partes. | 
| [parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retornar o valor de um parâmetro descrito na definição de seu aplicativo lógico. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Retornar a saída de um gatilho em tempo de execução ou de outros pares de nome e valor JSON. Confira também [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Retornar a saída `body` de um gatilho em tempo de execução. Confira [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Retornar um único valor correspondente a um nome de chave nas saídas dos gatilhos *form-data* ou *form-encoded*. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Retornar o corpo de uma parte específica na saída de várias partes de um gatilho. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Criar uma matriz com valores correspondentes a um nome de chave nas saídas de gatilho *form-data* ou *form-encoded*. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retornar a saída de um gatilho em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [variáveis](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Retornar o valor de uma variável especificada. | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Retornar todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funções de análise de URI

Para trabalhar com URIs (Uniform Resource Identifier) e obter vários valores das propriedades desses URIs, você pode usar estas funções de análise de URI. Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de análise de URI | Tarefa | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Retornar o valor `host` de um URI (Uniform Resource Identifier). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Retornar o valor `path` de um URI (Uniform Resource Identifier). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Retornar os valores `path` e `query` de um URI (Uniform Resource Identifier). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Retornar o valor `port` de um URI (Uniform Resource Identifier). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Retornar o valor `query` de um URI (Uniform Resource Identifier). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Retornar o valor `scheme` de um URI (Uniform Resource Identifier). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funções JSON e XML

Para trabalhar com objetos JSON e nós XML, você pode usar estas funções de manipulação. Para ver a referência completa de cada função, confira o [artigo de referência alfabética](../logic-apps/workflow-definition-language-functions-reference.md).

| Função de manipulação | Tarefa | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicionar uma propriedade e seu valor, ou par nome-valor, a um objeto JSON e retornar o objeto atualizado. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retornar o primeiro valor não nulo de um ou mais parâmetros. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Remover uma propriedade de um objeto JSON e retornar o objeto atualizado. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Definir o valor da propriedade de um objeto JSON e retornar o objeto atualizado. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores. | 
||| 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Workflow Definition Language actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md) (Ações e gatilhos da Linguagem de Definição de Fluxo de Trabalho)
* Saiba mais sobre como criar e gerenciar Aplicativos Lógicos de forma programática com a [API REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
