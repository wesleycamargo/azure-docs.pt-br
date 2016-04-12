<properties 
	pageTitle="Operadores e consultas da Análise no Application Insights" 
	description="Referência dos operadores usados para fazer consultas na Análise, a ferramenta de pesquisa avançada do Application Insights." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>

# Consultas na Análise


A [Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

Uma consulta sobre a telemetria é composta de uma referência a um fluxo de origem, seguida de um pipeline de filtros. Por exemplo:


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
Cada filtro prefixado pelo caractere de barra vertical `|` é uma instância de um *operador* com alguns parâmetros. A entrada do operador é a tabela que é o resultado do pipeline anterior. Na maioria dos casos, os parâmetros são [expressões escalares](app-insights-analytics-scalars.md) sobre as colunas da entrada. Em alguns casos, os parâmetros são os nomes das colunas da entrada e, em outros casos, o parâmetro é uma segunda tabela. O resultado de uma consulta é sempre uma tabela, mesmo que ela tenha somente uma coluna e uma linha.

Uma consulta pode ser prefixada por uma ou mais [cláusulas let](#let-clause), que definem escalares, tabelas ou funções que podem ser usadas dentro da consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` é usado nos exemplos de consulta a seguir para indicar a tabela de origem ou o pipeline anterior.

## operador count

O operador `count` retorna o número de registros (linhas) no conjunto de registros de entrada.

**Sintaxe**

    T | count

**Argumentos**

* *T*: os dados de tabela cujos registros serão contados.

**Retorna**

Essa função retorna uma tabela com um único registro e uma coluna do tipo `long`. O valor da célula única é o número de registros em *T*.

**Exemplo**

```AIQL
requests | count
```



## operador extend

     T | extend duration = stopTime - startTime

Acrescente uma ou mais colunas calculadas a uma tabela.


**Sintaxe**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome das colunas a serem adicionadas. 
* *Expression:* um cálculo sobre as colunas existentes.

**Retorna**

Uma cópia da tabela de entrada com colunas adicionais especificadas.

**Dicas**

* Use [`project`](#project-operator) em vez disso, se também quiser remover ou renomear algumas colunas.
* Não use `extend` simplesmente para obter um nome mais curto a ser usado em uma expressão longa. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    As colunas nativas da tabela foram indexadas. Seu novo nome define uma coluna adicional que não é indexada, assim, a consulta provavelmente terá execução mais lenta.

**Exemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## operador join

    Table1 | join (Table2) on CommonColumn

Mescla as linhas das duas tabelas fazendo a correspondência entre valores da coluna especificada.


**Sintaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Table1* - o 'lado esquerdo' da junção.
* *Table2* - o 'lado direito' da junção. Pode ser uma expressão de consulta aninhada que gera uma tabela.
* *CommonColumn* - uma coluna que tem o mesmo nome nas duas tabelas.
* *Kind* - especifica como deve ser feita a correspondência entre as linhas das duas tabelas.

**Retorna**

Uma tabela com:

* Uma coluna para cada coluna em cada uma das duas tabelas, incluindo as chaves correspondentes. As colunas do lado direito serão automaticamente renomeadas se houver conflitos de nome.
* Uma linha para cada correspondência entre as tabelas de entrada. Uma correspondência é uma linha selecionada de uma tabela que tem o mesmo valor para todos os campos `on` como uma linha na outra tabela. 

* `Kind` não especificado

    Somente uma linha do lado esquerdo é correspondida para cada valor da chave `on`. A saída contém uma linha para cada correspondência dessa linha com linhas da direita.

* `Kind=inner`
 
     Há uma linha na saída para cada combinação de linhas correspondentes da esquerda e da direita.

* `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)

     Além das correspondências internas, há uma linha para cada linha à esquerda (e/ou à direita), mesmo que ela não tenha uma correspondência. Nesse caso, as células de saída sem correspondência contêm valores nulos.

* `kind=leftanti`

     Retorna todos os registros do lado esquerdo que não têm correspondências da direita. A tabela de resultados tem apenas as colunas do lado esquerdo.
 
Se houver várias linhas com os mesmos valores para esses campos, você obterá linhas para todas as combinações.

**Dicas**

Para obter o melhor desempenho:

* Use `where` e `project` para reduzir o número de linhas e colunas nas tabelas de entrada, antes de `join`. 
* Se uma tabela sempre for menor do que a outra, use-a como o lado esquerdo (com barras verticais) da junção.
* As colunas para a correspondência de junção devem ter o mesmo nome. Use o operador de projeto se for necessário renomear uma coluna em uma das tabelas.

**Exemplo**

Obtenha atividades estendidas a partir de um log em que algumas entradas marcam o início e o fim de uma atividade.

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


## cláusula Let

**Let de tabela - nomeando uma tabela**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Let escalar - nomeando um valor**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Let lambda - nomeando uma função**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

Uma cláusula let associa um nome a um resultado de tabela, um valor escalar ou uma função. A cláusula é um prefixo para uma consulta e o escopo da associação é essa consulta. (Let não fornece uma maneira de nomear itens que você usa mais tarde na sessão.)

**Sintaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-insights-analytics-scalars.md#dynamic-type)
* *plain\_query:* uma consulta não prefixada por uma cláusula let.

**Exemplos**




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


Self-join:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

## operador limit

     T | limit 5

Retorna até o número especificado de linhas da tabela de entrada. Não há garantia de quais registros serão retornados. (Para retornar registros específicos, use [`top`](#top-operator)).

**Alias** `take`

**Sintaxe**

    T | limit NumberOfRows


**Dicas**

`Take` é uma maneira simples e eficiente de ver um exemplo dos resultados quando você trabalha de forma interativa. Lembre-se de que não há garantia quanto à produção de linhas específicas ou de sua produção em uma ordem específica.

Há um limite implícito quanto ao número de linhas retornadas ao cliente, mesmo que você não use `take`. Para aumentar esse limite, use a opção de solicitação do cliente `notruncation`.



## operador mvexpand

    T | mvexpand listColumn 

Expande uma lista de uma célula dinamicamente tipada (JSON) para que cada entrada tenha uma linha separada. Todas as outras células em uma linha expandida são duplicadas.

(Confira também [`summarize makelist`](#summarize-operator), que executa a função oposta).

**Exemplo**

Suponha que a tabela de entrada seja:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

O resultado é:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**Sintaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumentos**

* *ColumnName:* no resultado, as matrizes da coluna nomeada são expandidas para várias linhas. 
* *ArrayExpression:* uma expressão que resulta em uma matriz. Se esse formato for usado, uma nova coluna será adicionada e a existente será preservada.
* *Name:* um nome para a nova coluna.
* *Typename:* converte a expressão expandida em um tipo específico
* *RowLimit:* o número máximo de linhas geradas de cada linha original. O padrão é 128.

**Retorna**

Várias linhas para cada um dos valores em qualquer matriz na coluna nomeada ou na expressão de matriz.

A coluna expandida sempre tem um tipo dinâmico. Use uma conversão como `todatetime()` ou `toint()` se desejar calcular ou agregar valores.

Há suporte para dois modos de expansões de recipiente de propriedades:

* `bagexpansion=bag`: os recipientes de propriedades são expandidos em recipientes de propriedades de entrada única. Essa é a expansão padrão.
* `bagexpansion=array`: os recipientes de propriedades são expandidos para estruturas de matriz `[`*chave*`,`*valor*`]` de dois elementos, permitindo o acesso uniforme a chaves e a valores (bem como, por exemplo, a execução de uma agregação de contagem distinta em nomes de propriedades). 

**Exemplos**


    exceptions | take 1 
    | mvexpand details[0]

Divide um registro de exceção em linhas para cada item no campo de detalhes.



## operador parse

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

Extrai valores de uma cadeia de caracteres. Pode usar a correspondência de expressões regulares ou simples.

Os elementos na cláusula `with` são comparados à cadeia de caracteres de origem sucessivamente. Cada elemento remove uma parte do texto de origem. Caso se trate de uma cadeia de caracteres simples, o cursor correspondente se moverá até a correspondência. Caso se trate de uma coluna com um nome de tipo, o cursor se moverá o suficiente para analisar o tipo especificado. (As correspondências de cadeias de caracteres se movem até ser encontrada uma correspondência para o próximo elemento.) Se for uma regex, será feita a correspondência com a expressão regular (e a coluna resultante sempre terá o tipo de cadeia de caracteres).

**Sintaxe**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**Argumentos**

* *T:* a tabela de entrada.
* *kind:* simples ou regex. O padrão é simples.
* *StringExpression:* uma expressão que é avaliada ou que pode ser convertida em uma cadeia de caracteres.
* *SimpleMatch:* uma cadeia de caracteres que corresponde à próxima parte do texto.
* *Column:* especifica a nova coluna à qual uma correspondência deverá ser atribuída.
* *Type:* especifica como analisar a próxima parte da cadeia de caracteres de origem.
* *Regex:* uma expressão regular para corresponder à próxima parte da cadeia de caracteres. 

**Retorna**

A tabela de entrada, estendida de acordo com a lista de Colunas.


**Exemplos**

O operador `parse` fornece uma maneira simplificada de `extend` uma tabela usando vários aplicativos `extract` na mesma expressão `string`. Isso é particularmente útil quando a tabela tem uma coluna `string` que contém diversos valores que você deseja dividir em colunas individuais, como uma coluna que foi produzida por uma instrução de rastreamento de desenvolvedor ("`printf`"/"`Console.WriteLine`").

No exemplo a seguir, suponha que a coluna `EventNarrative` da tabela `StormEvents` contenha cadeias de caracteres no formato `{0} at {1} crested at {2} feet around {3} on {4} {5}`. A operação a seguir estenderá a tabela com duas colunas: `SwathSize` e `FellLocation`.


|EventNarrative|
|---|
|Green River em Brownsville com altura máxima de 5,7 m por volta das 0930EST em 12 de dezembro. O estágio de inundação em Brownsville é de 5 m. Pequenas inundações ocorrem nesse nível. O rio transborda pelas barragens e algumas das margens inferiores, além de terras planas cultivadas.|
|Rolling Fork River em Boston com altura máxima de 11,9 m por volta das 1700EST em 12 de dezembro. O estágio de inundação em Boston é de 10,6 m. Pequenas inundações ocorrem nesse nível e afetam terras planas cultivadas.|
|Green River em Woodbury com altura máxima de 11,1 m por volta das 0600EST em 16 de dezembro. O estágio de inundação em Woodbury é de 10 m. Pequenas inundações ocorrem nesse nível, com terras planas em torno da cidade de Woodbury cobertas pelas águas.|
|The Ohio River em Tell City com altura máxima de 11, 8 m por volta das 0700EST em 18 de dezembro. O estágio de inundação em Tell City é de 11,5 m. Nesse nível, o rio começa a transbordar nas margens acima da escala. Inundações em Indiana Highway 66 entre Rome e Derby.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|Nome do Rio|Local padrão|Altura|Hora|Mês|Dia|
|---|---|---|---|---|---|
|Green River | Woodbury |11,1| 0600EST | Dezembro|16|
|Rolling Fork River | Boston |11,9| 1700EST | Dezembro|12|
|Green River | Brownsville |5,7| 0930EST | Dezembro|12|
|Ohio River | Tell City |11,8| 0700EST | Dezembro|18|

Também é possível fazer a correspondência usando expressões regulares. Isso produz o mesmo resultado, mas todas as colunas de resultados têm o tipo de cadeia de caracteres:

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## operador project

    T | project cost=price*quantity, price

Selecione as colunas a serem incluídas, renomeadas ou removidas e insira novas colunas calculadas. A ordem das colunas no resultado é especificada pela ordem dos argumentos. Somente as colunas especificadas nos argumentos são incluídas no resultado: as demais colunas na entrada serão removidas. (Confira também `extend`).


**Sintaxe**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome de uma coluna que deve aparecer na saída. Se não houver uma *Expression*, uma coluna com esse nome deverá aparecer na entrada. 
* *Expression:* expressão escalar opcional que faz referência às colunas de entrada. 

    É válido retornar uma nova coluna calculada com o mesmo nome que uma coluna existente na entrada.

**Retorna**

Uma tabela que tem as colunas nomeadas como argumentos e a mesma quantidade de linhas da tabela de entrada.

**Exemplo**

O exemplo a seguir mostra vários tipos de manipulação que podem ser feitos usando o operador `project`. A tabela de entrada `T` tem três colunas de tipo `int`: `A`, `B` e `C`.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```



## operador range

    range LastWeek from ago(7d) to now() step 1d

Gera uma tabela de coluna única de valores. Observe que ele não tem uma entrada de pipeline.

|SemanaPassada|
|---|
|2015-12-05 09:10:04,627|
|2015-12-06 09:10:04,627|
|...|
|2015-12-12 09:10:04,627|



**Sintaxe**

    range ColumnName from Start to Stop step Step

**Argumentos**

* *ColumnName:* o nome da única coluna na tabela de saída.
* *Start:* o menor valor na saída.
* *Stop:* o valor mais elevado que está sendo gerado na saída (ou um limite para o valor mais elevado, se *step* passar por esse valor).
* *Step:* a diferença entre dois valores consecutivos. 

Os argumentos devem ser valores numéricos, de data ou de período de tempo. Eles não podem referenciar as colunas de nenhuma tabela. (Se quiser calcular o intervalo com base em uma tabela de entrada, use a [função *range*](app-insights-analytics-scalars.md#range), talvez com o [operador mvexpand](#mvexpand-operator)).

**Retorna**

Uma tabela com uma única coluna chamada *ColumnName*, cujos valores são *Start*, *Start* + *Step*, ... até *Stop* inclusive.

**Exemplo**

```AIQL
range Steps from 1 to 8 step 3
```

Uma tabela com uma única coluna chamada `Steps`, cujo tipo é `long` e cujos valores são `1`, `4` e `7`.

**Exemplo**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Uma tabela de meia-noite nos últimos sete dias. A função bin (piso) reduz cada horário ao início do dia.

**Exemplo**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Mostra como o operador `range` pode ser usado para criar uma pequena tabela de dimensões ad hoc que é então usada para introduzir zeros onde os dados de origem não têm valores.

## operador reduce

    exceptions | reduce by outerMessage

Tenta agrupar registros semelhantes. Para cada grupo, o operador envia o `Pattern` que acredita que descreva melhor o grupo e o `Count` de registros nesse grupo.


![](./media/app-insights-analytics-queries/reduce.png)

**Sintaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName:* a coluna a ser examinada. Deve ser do tipo cadeia de caracteres.
* *Threshold:* um valor no intervalo {0..1}. O padrão é 0,001. Para entradas grandes, o limite deve ser pequeno. 

**Retorna**

Duas colunas, `Pattern` e `Count`. Em muitos casos, Pattern será um valor completo da coluna. Em alguns casos, pode identificar termos comuns e substituir as partes variáveis por '*'.

Por exemplo, o resultado de `reduce by city` pode incluir:

|Padrão | Contagem |
|---|---|
| San * | 5182 |
| Santo * | 2846 |
| Moscou | 3726 |
| * -em- * | 2730 |
| Paris | 27163 |


## renderizar política

    T | render [ table | timechart  | barchart | piechart ]

A renderização instrui a camada de apresentação sobre como mostrar a tabela. Ela deve ser o último elemento do pipe. É uma alternativa conveniente ao uso dos controles de exibição, permitindo que você salve uma consulta com um método de apresentação específico.


## operador sort 

    T | sort by country asc, price desc

Classifica as linhas da tabela de entrada em ordem de acordo com uma ou mais colunas.

**Alias** `order`

**Sintaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* a tabela de entrada a ser classificada.
* *Column:* coluna *T* de acordo com a qual a classificação deve ser feita. O tipo dos valores deve ser numérico, de data, hora ou cadeia de caracteres.
* `asc` Classificar em ordem crescente, do mais baixo para o mais alto. O padrão é `desc`, em ordem decrescente do mais alto para o mais baixo.

**Exemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas as linhas na tabela Traces que têm uma `ActivityId` específica, classificadas por seu carimbo de data/hora.

## operador summarize

Produz uma tabela que agrega o conteúdo da tabela de entrada.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Uma tabela que mostra o número, a duração média da solicitação e o conjunto de cidades em cada país. Há uma linha na saída para cada país distinto. As colunas de saída mostram a contagem, a duração média, as cidades e o país. Todas as outras colunas de entrada são ignoradas.


    T | summarize count() by price_range=bin(price, 10.0)

Uma tabela que mostra quantos itens têm preços em cada intervalo [0,10,0], [10,0,20,0] e assim por diante. Este exemplo tem uma coluna para a contagem e um para a faixa de preços. Todas as outras colunas de entrada são ignoradas.

[Mais exemplos](app-insights-analytics-aggregations.md).



**Sintaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Column:* nome opcional para uma coluna de resultados. Usa como padrão um nome derivado da expressão.
* *Aggregation:* uma chamada para uma [função de agregação](app-insights-analytics-aggregations.md), como `count()` ou `avg()`, com nomes de coluna como argumentos. Confira a [lista de funções de agregação](app-insights-analytics-aggregations.md).
* *GroupExpression:* uma expressão sobre as colunas que fornece um conjunto de valores distintos. Normalmente, é um nome de coluna que já fornece um conjunto restrito de valores ou `bin()` com uma coluna numérica ou de hora como argumento. 

Se você fornecer uma expressão numérica ou de hora sem usar `bin()`, a Análise a aplicará automaticamente com um intervalo de `1h` para horas ou de `1.0` para números.

Se você não fornecer um *GroupExpression*, toda a tabela será resumida em uma única linha de saída.



**Retorna**

As linhas de entrada são organizadas em grupos com os mesmos valores das expressões `by`. Em seguida, as funções de agregação especificadas são calculadas sobre cada grupo, produzindo uma linha para cada grupo. O resultado contém as colunas `by` e também pelo menos uma coluna para cada agregação calculada. (Algumas funções de agregação retornam várias colunas.)

O resultado tem a mesma quantidade de linhas das diferentes combinações de valores `by`. Se quiser resumir intervalos de valores numéricos, use `bin()` para reduzir os intervalos a valores distintos.

**Observação**

Embora você possa fornecer expressões aleatórias para as expressões de agregação e de agrupamento, é mais eficiente usar nomes de coluna simples ou aplicar `bin()` a uma coluna numérica.



## operador take

Alias de [limit](#limit-operator)


## operador top

    T | top 5 by Name desc

Retorna os primeiros *N* registros classificados pelas colunas especificadas.


**Sintaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argumentos**

* *NumberOfRows:* o número de linhas de *T* a serem retornadas.
* *Sort\_expression:* uma expressão de acordo com a qual as linhas devem ser classificadas. Normalmente é apenas um nome de coluna. Você pode especificar mais de um sort\_expression.
* `asc` ou `desc` (o padrão) pode surgir para controlar se a seleção é realmente da parte "inferior" ou "superior" do intervalo.


**Dicas**

`top 5 by name` é superficialmente equivalente a `sort by name | take 5`. No entanto, é executado mais depressa e sempre retorna resultados classificados, enquanto `take` não oferece essa garantia.


## operador union

     Table1 | union Table2, Table3

Usa duas ou mais tabelas e retorna as linhas de todas elas.

**Sintaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Table1*, *Table2* ...
 *  O nome de uma tabela, como `events`, ou
 *  Uma expressão de consulta, como `(events | where id==42)`
 *  Um conjunto de tabelas especificadas com um curinga. Por exemplo, `E*` formaria a união de todas as tabelas no banco de dados cujos nomes começam por `E`.
* `kind`: 
 * `inner` - o resultado tem o subconjunto de colunas que são comuns a todas as tabelas de entrada.
 * `outer` - o resultado tem todas as colunas que ocorrem em qualquer uma das entradas. As células que não foram definidas por uma linha de entrada são definidas como `null`.
* `withsource=`*ColumnName:* se especificado, a saída incluirá uma coluna chamada *ColumnName*, cujo valor indica qual tabela de origem contribuiu com cada linha.

**Retorna**

Uma tabela com tantas linhas quanto as que existem em todas as tabelas de entrada.

**Exemplo**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
União de todas as tabelas cujos nomes começam por "tt".


**Exemplo**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
O número de usuários distintos que produziram um evento `exceptions` ou um evento `traces` no dia anterior. No resultado, a coluna “SourceTable” indicará "Query" ou "Command".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versão mais eficiente produz o mesmo resultado. Ela filtra cada tabela antes de criar a união.

## operador where

     T | where fruit=="apple"

Filtra uma tabela para o subconjunto de linhas que satisfazem a um predicado.

**Alias** `filter`

**Sintaxe**

    T | where Predicate

**Argumentos**

* *T:* a entrada de tabela cujos registros devem ser filtrados.
* *Predicate:* uma [expressão](app-insights-analytics-scalars.md#boolean) `boolean` nas colunas *T*. É avaliado para cada linha em *T*.

**Retorna**

As linhas em *T* para as quais o *Predicate* é `true`.

**Dicas**

Para obter o desempenho mais rápido:

* **Use comparações simples** entre os nomes de coluna e as constantes. ('Constante' significa constante ao longo da tabela. Portanto, `now()` e `ago()` estão OK, bem como valores escalares atribuídos usando uma cláusula [`let`](#let-clause)).

    Por exemplo, prefira `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Termos mais simples primeiro**: se houver várias cláusulas unidas com `and`, coloque primeiro as cláusulas que envolvem apenas uma coluna. Assim, `Timestamp > ago(1d) and OpId == EventId` é melhor do que o oposto.


**Exemplo**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros que existem há menos de uma hora e são provenientes da Origem chamada "Kuskus" e têm duas colunas de mesmo valor.

Observe que colocamos a comparação entre duas colunas por último, pois ela não pode utilizar o índice e força uma verificação.





[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!-----------HONumber=AcomDC_0330_2016-->