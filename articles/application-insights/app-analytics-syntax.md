<properties 
	pageTitle="Instruções na Análise de Aplicativos" 
	description="Consultas, expressões e instruções LET na Análise de Aplicativos, a poderosa ferramenta de pesquisa do Application Insights." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Instruções na Análise de Aplicativos

A [Análise de Aplicativos](app-analytics.md) é um poderoso mecanismo de pesquisa para sua telemetria do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise de Aplicativos, o CSL.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Modelo de dados

Em CSL:

* Um *banco de dados* contém zero ou mais *tabelas* nomeadas.
* Um *tabela* contém:
 * Uma ou mais *colunas* nomeadas. Cada coluna contém um *tipo.*
 * Uma ou mais *linhas.*
* Cada linha contém uma ou mais *células,* uma para cada coluna da tabela.
* Uma célula pode conter um valor do tipo da coluna, ou `null`.


## Nomes de entidade

Cada coluna, tabela ou banco de dados tem um nome exclusivo em seu contêiner.

Faça referência a uma entidade pelo seu nome (se o contexto não for ambíguo) ou qualificada por seu contêiner. Por exemplo, `MyColumn` também pode ser referenciado como `MyTable.MyColumn` ou `MyDatabase.MyTable.MyColumn`.

Os nomes podem ter até 1.024 caracteres. Eles diferenciam maiúsculas de minúsculas e podem conter letras, dígitos e sublinhados (`_`).

Além disso, os nomes podem ser colocados entre aspas para que possam conter outros caracteres. Por exemplo:

|||
|---|---|
|`['path\\file\n\'x\'']` | Use `` para caracteres de escape|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| Sem escapes – `` é a literal|
|`[@"\now & then"]` | |
|`[where]` | Usando uma palavra-chave de linguagem como um nome|

Os nomes também podem ser qualificados com o nome do banco de dados (veja [Consultas entre bancos de dados](#cross-database-queries))

```
database("MyDb").Table
```


## Instruções

Há quatro tipos de instruções em CSL:

### Consultas de dados
  
Solicitações somente leitura sobre os dados armazenados na Análise de Aplicativos. Por exemplo:

* `event` – Retorne todos os registros na tabela chamada “event”.
* `event | count` – Retorne um número de registros em “event”.

    
## Instruções LET

#### Visão geral
É possível prefixar uma instrução de consulta de dados por uma ou mais instruções LET. Isso permite associar um nome (um nome de entidade válido) a uma expressão. Nomes definidos por uma instrução LET podem ser usados uma ou mais vezes na instrução de consulta de dados a seguir.

Uma instrução LET pode associar um nome a expressões da seguinte variante:
1. Escalar
2. Dados tabulares 

Ao associar a Dados tabulares – é possível promover a instrução LET para uma “exibição” – isso permitirá que a instrução participe das operações “union *”.

#### Sintaxe

`let <name> = <expression>`

<expression> pode ser uma declaração lambda com zero, um ou mais argumentos:

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Exemplos: o seguinte exemplo associa o nome “x” à literal “1” do escalar:

```
let x=1;
range y from x to x step x
```

O seguinte exemplo associa uma única hora de logs ao nome Window e executa uma autojunção em Window:


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

Os dois exemplos a seguir mostram o uso de uma instrução LET com uma expressão lambda:


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

Usando a palavra-chave “view” para promover instruções LET para uma Exibição que participa de operações union *.


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Instrução RESTRICT

#### Visão geral
É possível restringir o acesso de consultas no escopo da lista de instruções usando a seguinte instrução:


```
restrict access to (<entity1, entity2, ...>);
```
 
A instrução permitirá o acesso apenas a essas entidades (instruções LET ou Entidades tabulares) que são permitidas pela instrução RESTRICT.
 
Exemplos:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Composição de consulta

Uma *consulta* segue o padrão:

- *origem* | *filtro* | *filtro* ...

Por exemplo:


```
Logs | where Timestamp > ago(1d) | count
```

* *Origem* é o nome de uma tabela de banco de dados ou de uma tabela de resultados definida anteriormente.
* Cada *filtro* invoca um operador de consulta, como `where` ou `count`, com os parâmetros apropriados. Os parâmetros podem ser *expressões escalares*, *consultas de dados* aninhadas ou nomes de coluna.

Por exemplo:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Instruções LET

Uma instrução LET pode ser usada para definir funções – chamadas de expressões que levam zero ou mais argumentos e que retornam valores. Instruções adicionais poderão, em seguida, “invocar” essas funções.

### Valores nomeados

Definem nomes para representar os valores escalares:


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Definem nomes para representar os resultados da consulta:


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

Isso é especialmente útil para uma autojunção:


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Funções nomeadas

Definem funções que retornam os resultados escalares:


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Definem funções que retornam os resultados da consulta:


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

Os parâmetros para funções nomeadas devem ser escalares.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->