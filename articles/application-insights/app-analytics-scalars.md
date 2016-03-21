<properties 
	pageTitle="Expressões escalares na Análise do Application Insights" 
	description="Números, cadeias de caracteres, expressões dinâmicas e tipos na Análise do Application Insights, a ferramenta de pesquisa avançada do Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2016" 
	ms.author="awills"/>


 
# Expressões escalares na Análise do Application Insights


A [Análise do Application Insights](app-analytics.md) é um poderoso mecanismo de pesquisa para sua telemetria do [Application Insights](app-insights-overview.md). Estas páginas descrevem a linguagem de consulta da Análise do Application Insights, o AIQL.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) <br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) <br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt) <br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



"Escalar" significa valores, como números ou cadeias de caracteres, que podem ocupar uma única célula em uma tabela AIQL. As expressões escalares são criadas de funções e operadores escalares e são avaliadas para valores escalares. `sqrt(score)/100 > target+2` é uma expressão escalar.

"Escalar" também inclui matrizes e objetos compostos, que também podem ser armazenados em uma única célula de banco de dados.

As expressões escalares são diferentes de [consultas](app-analytics-queries.md), cujos resultados são tabelas.

## Escalares

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

Os tipos que recebem suporte são:

| Tipo | Nomes adicionais | Tipo equivalente do .NET |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Conversões

Você pode converter de um tipo para outro. Em geral, se a conversão fizer sentido, ela funcionará:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### Comparações escalares

||
---|---
`<` |Menor
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 
`in`| O operando à direita é uma matriz (dinâmica) e o operando à esquerda é igual a um de seus elementos.
`!in`| O operando à direita é uma matriz (dinâmica) e o operando à esquerda não é igual a qualquer um de seus elementos.




### gettype

**Retorna**

Uma cadeia de caracteres que representa o tipo de armazenamento subjacente de seu argumento único. Isso é particularmente útil quando há valores do tipo `dynamic`: nesse caso, `gettype()` revelará como um valor é codificado.

**Exemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**Sintaxe**

    hash(source [, mod])

**Argumentos**

* *source*: o escalar de origem no qual o hash é calculado.
* *mod*: o valor de módulo a ser aplicado no resultado do hash.

**Retorna**

O valor de xxhash (longo) do escalar especificado, módulo do valor de mod fornecido (se for especificado).

**Exemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

A função `iff()` avalia o primeiro argumento (o predicado) e retorna o valor do segundo ou do terceiro argumento, dependendo se o predicado for `true` ou `false`. O segundo e o terceiro argumentos devem ser do mesmo tipo.

**Sintaxe**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicate:* uma expressão que é avaliada como um valor `boolean`.
* *ifTrue:* uma expressão que é avaliada e tem seu valor retornado da função se *predicate* for avaliado como `true`.
* *ifFalse:* uma expressão que é avaliada e tem seu valor retornado da função se *predicate* for avaliado como `false`.

**Retorna**

Essa função retorna o valor de *ifTrue* se *predicate* for avaliado como `true` ou retorna o valor de *ifFalse* caso ocorra o contrário.

**Exemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Aceita um único argumento e informa se ele é nulo.

**Sintaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Retorna**

True ou false dependendo se o valor for nulo ou não nulo.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|verdadeiro
|parsejson("")|false
|parsejson("{}")|false

**Exemplo**

    T | where isnotnull(PossiblyNull) | count

Observe que há outras maneiras de conseguir esse efeito:

    T | summarize count(PossiblyNull)




## Booliano 

### Literais boolianos

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Operadores boolianos

	and 
    or 

    

## Números

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Literais numéricos

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Operadores aritméticos

|| |
|---|-------------|
| + | Adicionar |
| - | Subtrair | | * | Multiplicar | | / | Dividir | | % | Módulo | || |`<` |Menor |`<=`|Menor ou Igual a |`>` |Maior |`>=`|Maior ou Igual a |`<>`|Diferente de |`!=`|Diferente de




### bin

Arredonda os valores até um número inteiro múltiplo de um determinado tamanho de compartimentalização. Muito usado na consulta [`summarize by`](app-analytics-queries.md#summarize-operator). Se você tiver um conjunto disperso de valores, eles serão agrupados em um conjunto menor de valores específicos.

Alias `floor`.

**Sintaxe**

     bin(value, roundTo)

**Argumentos**

* *value:* um número, uma data ou um período. 
* *roundTo:* o "tamanho da compartimentalização". Um número, uma data ou um período que divide *value*. 

**Retorna**

O múltiplo mais próximo de *roundTo* abaixo de *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemplos**

Expressão | Resultado
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


A expressão a seguir calcula um histograma de durações, com um tamanho de partição de um segundo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

Um alias para [`bin()`](#bin).


### rand

Um gerador de número aleatório.

* `rand()` - um número real entre 0,0 e 1,0
* `rand(n)` - um número inteiro entre 0 e n-1




### sqrt

A função da raiz quadrada.

**Sintaxe**

    sqrt(x)

**Argumentos**

* *x:* um número real >= 0.

**Retorna**

* Um número positivo, como `sqrt(x) * sqrt(x) == x`
* `null` se o argumento for negativo ou não puder ser convertido em um valor `real`. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Data e hora


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### Literais de data e hora

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Os horários estão sempre em UTC. A omissão da data fornece um horário de hoje.
`now()`|A hora atual.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 dias
`1.5h`|1,5 hora 
`30m`|30 minutos
`10s`|10 segundos
`0.1s`|0,1 segundo
`100ms`| 100 milissegundos
`10microsecond`|
`1tick`|100 ns
`time("15 seconds")`|
`time("2")`| 2 dias
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Expressões de data e hora

Expressão |Resultado
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Menor
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 




### ago

Subtrai o período fornecido da hora atual UTC. Assim como `now()`, essa função pode ser usada várias vezes em uma instrução e a hora UTC referenciada será a mesma para todas as instanciações.

**Sintaxe**

    ago(a_timespan)

**Argumentos**

* *a\_timespan*: intervalo a ser subtraído da hora UTC atual (`now()`).

**Retorna**

    now() - a_timespan

**Exemplo**

Todas as linhas com um carimbo de data/hora na última hora:

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

O número inteiro de dias desde o último domingo, como um `timespan`.

**Sintaxe**

    dayofweek(a_date)

**Argumentos**

* `a_date`: um `datetime`.

**Retorna**

O `timespan` desde a meia-noite no início do último domingo, arredondado para baixo até um número inteiro de dias.

**Exemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

Obtenha o número do mês (1 a 12) de um datetime.

**Exemplo**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Obter o ano a partir de um datetime.

**Exemplo**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

A hora UTC atual, opcionalmente separada por um determinado período. Essa função pode ser usada várias vezes em uma instrução, e a hora referenciada será a mesmo para todas as instanciações.

**Sintaxe**

    now([offset])

**Argumentos**

* *offset:* um `timespan`, acrescentado à hora UTC atual. Padrão: 0.

**Retorna**

A hora UTC atual como um `datetime`.

    now() + offset

**Exemplo**

Determina o intervalo desde o evento identificado pelo predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

O início do mês que contém a data.

### startofyear

    startofyear(date)

O início do ano que contém a data.


### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## Cadeia de caracteres

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Literais de cadeia de caracteres

As regras são as mesmas do JavaScript.

As cadeias de caracteres podem ser colocadas entre aspas únicas ou duplas.

Barra invertida (``) é usada para escapar caracteres como `\t` (guia), `\n` (nova linha) e instâncias do caractere de aspas.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Literais de cadeia de caracteres ofuscados

Os literais de cadeia de caracteres ofuscados são cadeias de caracteres que a Análise do AI irá obscurecer ao exibir a saída de cadeia de caracteres (por exemplo, durante o rastreamento). O processo de ofuscação substitui todos os caracteres ofuscados por um caractere de início (`*`).

Para formar um literal de cadeia de caracteres ofuscado, preceda `h` ou 'H'. Por exemplo:

```
h'hello'
h@'world' 
h"hello"
```

### Comparações de cadeias de caracteres

Operador|Descrição|Diferencia maiúsculas de minúsculas|Exemplo verdadeiro
---|---|---|---
`==`|É igual a |Sim| `"aBc" == "aBc"`
`<>`|Diferente de|Sim| `"abc" <> "ABC"`
`=~`|É igual a |Não| `"abc" =~ "ABC"`
`!~`|Diferente de |Não| `"aBc" !~ "xyz"`
`has`|O lado direito (RHS) é um termo completo no lado esquerdo (LHS)|Não| `"North America" has "america"`
`!has`|RHS não é um termo completo no LHS|Não|`"North America" !has "amer"` 
`contains` | RHS ocorre como uma subsequência do LHS|Não| `"FabriKam" contains "BRik"`
`!contains`| RHS não ocorre no LHS|Não| `"Fabrikam" !contains "xyz"`
`containscs` | RHS ocorre como uma subsequência do LHS|Sim| `"FabriKam" contains "Kam"`
`!containscs`| RHS não ocorre no LHS|Sim| `"Fabrikam" !contains "Kam"`
`startswith`|RHS é uma subsequência inicial do LHS.|Não|`"Fabrikam" startswith "fab"`
`matches regex`|LHS contém uma correspondência para o RHS|Sim| `"Fabrikam" matches regex "b.*k"`


Use `has` ou `in` se você estiver testando a presença de um termo lexical completo, ou seja, um símbolo ou palavra alfanumérica delimitada por caracteres não alfanuméricos ou pelo início ou término do campo. `has` executa mais rápido do que `contains` ou `startswith`. A primeira dessas consultas é executada com mais rapidez:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta as ocorrências de uma subcadeia de caracteres em uma cadeia de caracteres. As correspondências de cadeia de caracteres simples podem se sobrepor; as correspondências de regex não.

**Sintaxe**

    countof(text, search [, kind])

**Argumentos**

* *text:* uma cadeia de caracteres.
* *search:* a cadeia de caracteres simples ou a [expressão regular](app-analytics-reference.md#regular-expressions) a ser correspondida em *text*.
* *kind:* `"normal"|"regex"` padrão `normal`. 

**Retorna**

O número de vezes que a cadeia de caracteres de pesquisa pode ser correspondida no contêiner. As correspondências de cadeia de caracteres simples podem se sobrepor; as correspondências de regex não.

**Exemplos**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (não 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtém uma correspondência para um [expressão regular](app-analytics-reference.md#regular-expressions) a partir de uma cadeia de caracteres de texto. Opcionalmente, converte a subcadeia de caracteres extraída para o tipo indicado.

**Sintaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* uma [expressão regular](app-analytics-reference.md#regular-expressions).
* *captureGroup:* uma constante `int` positiva que indica o grupo de captura para extração. 0 significa toda a correspondência, um para o valor correspondido pelo primeiro '('parêntese')' na expressão regular, dois ou mais para os parênteses subsequentes.
* *text:* um `string` para pesquisa.
* *typeLiteral:* um literal de tipo opcional (por exemplo, `typeof(long)`). Se for fornecido, a subcadeia de caracteres extraída será convertida para esse tipo. 

**Retorna**

Se *regex* encontrar uma correspondência em *text*: a subcadeia de caracteres correspondida com base no grupo de captura indicado *captureGroup*, convertido, opcionalmente, em *typeLiteral*.

Se não houver correspondência, ou se a conversão do tipo falhar: `null`.

**Exemplos**

A cadeia de caracteres de exemplo `Trace` é pesquisada em busca de uma definição para `Duration`. A correspondência é convertida em `real`, multiplicada por uma constante de tempo (`1s`) para que `Duration` seja do tipo `timespan`. Neste exemplo, é igual a 123,45 segundos:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Este exemplo é equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True se o argumento for uma cadeia de caracteres vazia ou nula. Confira também [isnull](#isnull).


**Sintaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Retorna**

Indica se o argumento é uma cadeia de caracteres vazia ou isnull.

|x|isempty(x)
|---|---
| "" | verdadeiro
|"x" | false
|parsejson("")|verdadeiro
|parsejson("")|false
|parsejson("{}")|false


**Exemplo**


    T | where isempty(fieldName) | count




### substitui

Substitua todas as correspondências de regex por outra cadeia de caracteres.

**Sintaxe**

    replace(regex, rewrite, text)

**Argumentos**

* *regex:* a [expressão regular](https://github.com/google/re2/wiki/Syntax) para pesquisar em *text*. Pode conter grupos de captura entre '('parênteses')'. 
* *rewrite:* o regex de substituição para qualquer correspondência feita por *matchingRegex*. Use `\0` para referir-se à correspondência inteira, `\1` para o primeiro grupo de captura `\2` etc. para grupos de captura subsequentes.
* *text:* uma cadeia de caracteres.

**Retorna**

*text* depois de substituir todas as correspondências de *regex* por avaliações de *rewrite*. As correspondências não se sobrepõem.

**Exemplo**

Esta instrução:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Tem os seguintes resultados:

| x | str | replaced|
|---|---|---|
| 1 | O número é 1,000000 | O número era: 1,000000|
| 2 | O número é 2,000000 | O número era: 2,000000|
| 3 | O número é 3,000000 | O número era: 3,000000|
| 4 | O número é 4,000000 | O número era: 4,000000|
| 5 | O número é 5,000000 | O número era: 5,000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide uma determinada cadeia de caracteres de acordo com um determinado delimitador e retorna uma matriz de cadeias de caracteres com as subcadeias de caracteres contidas. Opcionalmente, uma subcadeia de caracteres específica pode ser retornada, se existir.

**Sintaxe**

    split(source, delimiter [, requestedIndex])

**Argumentos**

* *source*: a cadeia de caracteres de origem que será dividida de acordo com o delimitador especificado.
* *delimiter*: o delimitador que será usado para dividir a cadeia de caracteres de origem.
* *requestedIndex*: um índice opcional baseado em zero `int`. Se for fornecido, a matriz de cadeias de caracteres retornada conterá a subcadeia de caracteres solicitada, se ela existir. 

**Retorna**

Uma matriz de cadeias de caracteres que contém as subcadeias de caracteres da cadeia de caracteres de origem fornecida, delimitadas pelo delimitador especificado.

**Exemplos**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

Concatena entre 1 e 16 argumentos, que devem ser cadeias de caracteres.

### strlen

    strlen("hello") == 5

Comprimento de uma cadeia de caracteres.

### substring

    substring("abcdefg", 1, 2) == "bc"

Extrai uma subcadeia de caracteres de uma cadeia de caracteres de origem fornecida a partir de um índice determinado. Opcionalmente, é possível especificar o comprimento da subcadeia de caracteres solicitada.

**Sintaxe**

    substring(source, startingIndex [, length])

**Argumentos**

* *source:* a cadeia de caracteres de origem a partir da qual a subcadeia de caracteres será extraída.
* *startingIndex:* a posição do caractere inicial com base em zero da subcadeia de caracteres solicitada.
* *lenght:* um parâmetro opcional que pode ser usado para especificar o número solicitado de caracteres na subcadeia de caracteres. 

**Retorna**

Uma subcadeia de caracteres a partir da cadeia de caracteres especificada. A subcadeia de caracteres começa na posição do caractere startingIndex (baseado em zero) e continua até o final da cadeia de caracteres ou do comprimento dos caracteres, se for especificado.

**Exemplos**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Converte uma cadeia de caracteres em letras minúsculas.

### toupper

    toupper("hello") == "HELLO"

Converte uma cadeia de caracteres em letras maiúsculas.



## GUIDs

    guid(00000000-1111-2222-3333-055567f333de)


## Matrizes e objetos - tipos dinâmicos

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Este é o resultado de uma consulta em uma exceção do Application Insights. O valor em `details` é uma matriz.

![](./media/app-analytics-scalars/310.png)

**Indexação:** Índice de matrizes e objetos, assim como no JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mas use `arraylength` e outras funções AIQL (não ".length"!)

**Conversão** Em alguns casos, é necessário converter um elemento que você extraiu de um objeto, pois seu tipo pode variar. Por exemplo, `summarize...to` precisa de um tipo específico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literais** Para criar uma matriz explícita ou um objeto de recipiente de propriedades, escreva-a como uma cadeia de caracteres JSON e a converta:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** para extrair e separar as propriedades de um objeto em linhas separadas, use mvexpand:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


**treepath:** para localizar todos os caminhos em um objeto complexo:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema:** para localizar o esquema mínimo que admite todos os valores da expressão na tabela:

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que o `indexer` é usado para marcar onde você deve usar um índice numérico. Nesse esquema, alguns caminhos válidos devem ser (supondo que esses índices de exemplo estejam dentro do intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### Literais de matriz e objeto

Para criar um literal dinâmico, use `parsejson` (alias `todynamic`) com um argumento de cadeia de caracteres JSON:

* `parsejson('[43, 21, 65]')` - uma matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - um único valor de tipo dinâmico contendo um número
* `parsejson('"21"')` - um único valor de tipo dinâmico contendo uma cadeia de caracteres

Observe que, ao contrário do JavaScript, JSON exige o uso de aspas duplas (`"`) ao redor de cadeias de caracteres. Portanto, é geralmente mais fácil citar literais de uma cadeia de caracteres codificada em JSON usando aspas simples (`'`).

Este exemplo cria um valor dinâmico e então usa seus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Operadores e funções sobre tipos dinâmicos

|||
|---|---|
| *value* `in` *array*| True se houver um elemento de *array* que = = *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True se não houver um elemento de *array* que = = *value*
|[`arraylength(`array`)`](#arraylength)| Null se não for uma matriz
|[`extractjson(`path,object`)`](#extractjson)|Usa o caminho para navegar no objeto.
|[`parsejson(`source`)`](#parsejson)| Transforma uma cadeia de caracteres JSON em um objeto dinâmico.
|[`range(`from,to,step`)`](#range)| Uma matriz de valores
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | Replica uma linha para cada valor em uma lista em uma célula especificada.
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |Infere o esquema de tipo a partir do conteúdo da coluna
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| Mescla os grupos de linhas e coloca os valores da coluna em uma matriz.
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | Mescla os grupos de linhas e coloca os valores da coluna em uma matriz, sem duplicação.

### Objetos dinâmicos em cláusulas let


As [cláusulas let](app-analytics-queries.md#let-clause) armazenam valores dinâmicos como cadeias de caracteres, portanto, essas duas cláusulas são equivalentes, e ambas precisam de `parsejson` (ou `todynamic`) antes de serem usadas:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

O número de elementos em uma matriz dinâmica.

**Sintaxe**

    arraylength(array)

**Argumentos**

* *array:* um valor `dynamic`.

**Retorna**

O número de elementos em *array* ou `null` se *array* não for uma matriz.

**Exemplos**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtenha um elemento especificado de um texto JSON usando uma expressão de caminho. Converta, opcionalmente, a cadeia de caracteres extraída para um tipo específico.


**Sintaxe**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Retorna**

Esta função executa uma consulta JsonPath em dataSource, que contém uma cadeia de caracteres JSON válida, convertendo, como opção, esse valor em outro tipo, dependendo do terceiro argumento.



**Exemplo**

A notação de ponto e a notação [colchetes] são equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### Expressões de caminho JSON

|||
|---|---|
|`$`|Objeto raiz|
|`@`|Objeto atual|
|`.` ou `[ ]` | Filho|
|`[ ]`|Subscrito de matriz|

*(No momento, não implementamos caracteres curinga, recursão, união ou fatias.)*


**Dicas de desempenho**

* Aplicar cláusulas where antes de usar `extractjson()`
* Considere o uso de uma correspondência da expressão regular com [extract](#extract). Isso pode ser executado muito mais rápido, e será eficaz se JSON for produzido a partir de um modelo.
* Use `parsejson()` se você precisar extrair mais de um valor de JSON.
* Considere analisar o JSON na ingestão declarando o tipo da coluna como dinâmica.



### parsejson

Interpreta um `string` como um [valor de JSON](http://json.org/)) e retorna o valor como `dynamic`. É superior ao uso de `extractjson()` quando você precisa extrair mais de um elemento de um objeto JSON composto.

**Sintaxe**

    parsejson(json)

**Argumentos**

* *json:* um documento JSON.

**Retorna**

Um objeto do tipo `dynamic` especificado por *json*.

**Exemplo**

No exemplo a seguir, quando `context_custom_metrics` é um `string` que se parece com isto:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

então o fragmento AIQL a seguir recupera o valor do slot `duration` no objeto e, a partir disso, recupera dois slots, `duration.value` e `duration.min` (`118.0` e `110.0`, respectivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

A função `range()` (não deve ser confundida com o operador `range`) gera uma matriz dinâmica que contém uma série de valores espaçados igualmente.

**Sintaxe**

    range(start, stop, step)

**Argumentos**

* *start:* o valor do primeiro elemento na matriz resultante. 
* *stop:* o valor do último elemento na matriz resultante, ou o valor mínimo que seja maior do que o último elemento na matriz resultante e dentro de um número inteiro múltiplo de *step* a partir de *start*.
* *step:* a diferença entre dois elementos consecutivos da matriz.

**Exemplos**

O exemplo a seguir retorna `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

O exemplo a seguir retorna uma matriz que contém todos os dias do ano de 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte uma cadeia de caracteres em um valor dinâmico.

### treepath

    treepath(dynamic_object)

Enumera todas as expressões de caminho que identificam folhas em um objeto dinâmico.

**Retorna**

Uma matriz de expressões de caminho.

**Exemplos**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Observe que "[0]" indica a presença de uma matriz, mas não especifica o índice usado por um caminho específico.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->