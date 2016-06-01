<properties 
	pageTitle="Referência da Análise no Application Insights" 
	description="Referência de instruções na Análise, a ferramenta de pesquisa avançada do Application Insights." 
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
	ms.date="04/18/2016" 
	ms.author="awills"/>

# Referência da Análise

[Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]


| | | | | 
|---|---|---|---|---
|[ago](#ago)|[dayofweek](#dayofweek)|[cláusula Let](#let-clause)|[rand](#rand)|[sum](#sum)
|[qualquer](#any)|[dcount](#dcount)|[operador limit](#limit-operator)|[range](#range)|[operador summarize](#summarize-operator)
|[argmax](#argmax)|[Objetos dinâmicos em cláusulas let](#dynamic-objects-in-let-clauses)|[log](#log)|[operador range](#range-operator)|[operador take](#take-operator)
|[argmin](#argmin)|[exp](#exp)|[makelist](#makelist)|[operador reduce](#reduce-operator)|[todatetime](#todatetime)
|[Operadores aritméticos](#arithmetic-operators)|[operador extend](#extend-operator)|[makeset](#makeset)|[renderizar política](#render-directive)|[todouble](#todouble)
|[Literais de matriz e objeto](#array-and-object-literals)|[extract](#extract)|[max](#max)|[substitui](#replace)|[todynamic](#todynamic)
|[arraylength](#arraylength)|[extractjson](#extractjson)|[min](#min)|[restringir cláusula](#restrict-clause)|[toint](#toint)
|[avg](#avg)|[floor](#floor)|[operador mvexpand](#mvexpand-operator)|[Comparações escalares](#scalar-comparisons)|[tolong](#tolong)
|[bin](#bin)|[getmonth](#getmonth)|[notempty](#notempty)|[operador sort](#sort-operator)|[tolower](#tolower)
|[Literais boolianos](#boolean-literals)|[gettype](#gettype)|[notnull](#notnull)|[split](#split)|[operador top](#top-operator)
|[Operadores boolianos](#boolean-operators)|[getyear](#getyear)|[now](#now)|[sqrt](#sqrt)|[totimespan](#totimespan)
|[buildschema](#buildschema)|[hash](#hash)|[Literais numéricos](#numeric-literals)|[startofmonth](#startofmonth)|[toupper](#toupper)
|[Conversões](#casts)|[iff](#iff)|[Literais de cadeia de caracteres ofuscados](#obfuscated-string-literals)|[startofyear](#startofyear)|[treepath](#treepath)
|[count](#count)|[isempty](#isempty)|[operador parse](#parse-operator)|[stdev](#stdev)|[operador union](#union-operator)
|[operador count](#count-operator)|[isnotempty](#isnotempty)|[parsejson](#parsejson)|[strcat](#strcat)|[variance](#variance)
|[countif](#countif)|[isnotnull](#isnotnull)|[percentile](#percentile)|[Comparações de cadeias de caracteres](#string-comparisons)|[operador where](#where-operator)
|[countof](#countof)|[isnull](#isnull)|[percentiles](#percentiles)|[Literais de cadeia de caracteres](#string-literals)
|[Expressões de data e hora](#date-and-time-expressions)|[operador join](#join-operator)|[operador project](#project-operator)|[strlen](#strlen)
|[Literais de data e hora](#date-and-time-literals)|[Expressões de caminho JSON](#json-path-expressions)|[project-away op](#project-away-operator)|[substring](#substring)


## Consultas e operadores

Uma consulta sobre a telemetria é composta de uma referência a um fluxo de origem, seguida de um pipeline de filtros. Por exemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro prefixado pelo caractere de barra vertical `|` é uma instância de um *operador* com alguns parâmetros. A entrada do operador é a tabela que é o resultado do pipeline anterior. Na maioria dos casos, os parâmetros são [expressões escalares](##scalars) sobre as colunas da entrada. Em alguns casos, os parâmetros são os nomes das colunas da entrada e, em outros casos, o parâmetro é uma segunda tabela. O resultado de uma consulta é sempre uma tabela, mesmo que ela tenha somente uma coluna e uma linha.

Consultas podem conter quebras de linha simples, mas são finalizadas por uma linha em branco. Elas podem conter comentários entre `//` e o final da linha.

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
> 

### operador count

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



### operador extend

     T | extend duration = stopTime - startTime

Acrescente uma ou mais colunas calculadas a uma tabela.


**Sintaxe**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome das colunas a serem adicionadas. [Nomes](#names) diferenciam maiúsculas de minúsculas e pode conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
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


### operador join

    Table1 | join (Table2) on CommonColumn

Mescla as linhas das duas tabelas fazendo a correspondência entre valores da coluna especificada.


**Sintaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Table1*: o “lado esquerdo” da junção.
* *Table2*: o “lado direito” da junção. Pode ser uma expressão de consulta aninhada que gera uma tabela.
* *CommonColumn*: uma coluna que tem o mesmo nome nas duas tabelas.
* *Kind*: especifica como deve ser feita a correspondência entre as linhas das duas tabelas.

**Retorna**

Uma tabela com:

* Uma coluna para cada coluna em cada uma das duas tabelas, incluindo as chaves correspondentes. As colunas do lado direito serão automaticamente renomeadas se houver conflitos de nome.
* Uma linha para cada correspondência entre as tabelas de entrada. Uma correspondência é uma linha selecionada de uma tabela que tem o mesmo valor para todos os campos `on` como uma linha na outra tabela. 

* `Kind` não especificado

    Somente uma linha do lado esquerdo é correspondida para cada valor da chave `on`. A saída contém uma linha para cada correspondência dessa linha com linhas da direita.

* `Kind=inner`
 
     Há uma linha na saída para cada combinação de linhas correspondentes da esquerda e da direita.

* `kind=leftouter` (`kind=rightouter` ou `kind=fullouter`)

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

### cláusula Let

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

Uma cláusula let associa um [nome](#names) a um resultado de tabela, um valor escalar ou uma função. A cláusula é um prefixo para uma consulta e o escopo da associação é essa consulta. (Let não fornece uma maneira de nomear itens que você usa mais tarde na sessão.)

**Sintaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *tipo:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
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

### operador limit

     T | limit 5

Retorna até o número especificado de linhas da tabela de entrada. Não há garantia de quais registros serão retornados. (Para retornar registros específicos, use [`top`](#top-operator).)

**Alias** `take`

**Sintaxe**

    T | limit NumberOfRows


**Dicas**

`Take` é uma maneira simples e eficiente de ver um exemplo dos resultados quando você trabalha de forma interativa. Lembre-se de que não há garantia quanto à produção de linhas específicas ou de sua produção em uma ordem específica.

Há um limite implícito quanto ao número de linhas retornadas ao cliente, mesmo que você não use `take`. Para aumentar o limite, use a opção de solicitação do cliente `notruncation`.



### operador mvexpand

    T | mvexpand listColumn 

Expande uma lista de uma célula dinamicamente tipada (JSON) para que cada entrada tenha uma linha separada. Todas as outras células em uma linha expandida são duplicadas.

(Confira também [`summarize makelist`](#summarize-operator), que executa a função oposta.)

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

* *ColumnName:* no resultado, as matrizes na coluna nomeada são expandidas para várias linhas. 
* *ArrayExpression:* uma expressão que resulta em uma matriz. Se esse formato for usado, uma nova coluna será adicionada e a existente será preservada.
* *Name:* um nome para a nova coluna.
* *Typename:* converte a expressão expandida em um tipo específico
* *RowLimit:* o número máximo de linhas geradas em cada linha original. O padrão é 128.

**Retorna**

Várias linhas para cada um dos valores em qualquer matriz na coluna nomeada ou na expressão de matriz.

A coluna expandida sempre tem um tipo dinâmico. Use uma conversão, como `todatetime()` ou `toint()`, se desejar calcular ou agregar valores.

Há suporte para dois modos de expansões de recipiente de propriedades:

* `bagexpansion=bag`: os recipientes de propriedades são expandidos como recipientes de propriedades de entrada única. Essa é a expansão padrão.
* `bagexpansion=array`: os recipientes de propriedades são expandidos como estruturas de matriz `[`*key*`,`*value*`]` de dois elementos, permitindo o acesso uniforme a chaves e valores (bem como, por exemplo, a execução de uma agregação de contagem distinta sobre nomes de propriedades). 

**Exemplos**


    exceptions | take 1 
    | mvexpand details[0]

Divide um registro de exceção em linhas para cada item no campo de detalhes.



### operador parse

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
* *StringExpression:* uma expressão que é avaliada ou pode ser convertida em uma cadeia de caracteres.
* *SimpleMatch:* uma cadeia de caracteres que corresponde à próxima parte do texto.
* *Column:* especifica a nova coluna à qual atribuir uma correspondência.
* *Type:* especifica como analisar a próxima parte da cadeia de caracteres de origem.
* *Regex:* uma expressão regular para corresponder à próxima parte da cadeia de caracteres. 

**Retorna**

A tabela de entrada, estendida de acordo com a lista de Colunas.


**Exemplos**

O operador `parse` fornece uma maneira simplificada de `extend` uma tabela usando vários aplicativos `extract` na mesma expressão `string`. Isso é particularmente útil quando a tabela tem uma coluna `string` que contém diversos valores que você deseja dividir em colunas individuais, como uma coluna que foi produzida por uma instrução de rastreamento de desenvolvedor ("`printf`"/"`Console.WriteLine`").

No exemplo a seguir, suponha que a coluna `EventNarrative` da tabela `StormEvents` contenha cadeias de caracteres no formato `{0} at {1} crested at {2} feet around {3} on {4} {5}`. A operação a seguir estenderá a tabela com duas colunas: `SwathSize` e `FellLocation`.


|EventNarrative|
|---|
|Green River em Brownsville com altura máxima de 5,7 m por volta das 0930EST em 12 de dezembro (The Green River at Brownsville crested at 18.8 feet around 0930EST on December 12). O estágio de inundação em Brownsville é de 5 m (Flood stage at Brownsville is 18 feet.). Pequenas inundações ocorrem nesse nível (Flood stage at Brownsville is 18 feet.). O rio transborda pelas barragens e algumas das margens inferiores, além de terras planas cultivadas (The river overflows lock walls and some of the lower banks, along with some agricultural bottom land).|
|Rolling Fork River em Boston com altura máxima de 11,9 m por volta das 1700EST em 12 de dezembro (The Rolling Fork River at Boston crested at 39.3 feet around 1700EST on December 12). O estágio de inundação em Boston é de 10,6 m (Flood stage at Boston is 35 feet). Pequenas inundações ocorrem nesse nível e afetam terras planas cultivadas (Minor flooding occurs at this level, with some agricultural bottom land covered).|
|Green River em Woodbury com altura máxima de 11,1 m por volta das 0600EST em 16 de dezembro (The Green River at Woodbury crested at 36.7 feet around 0600EST on December 16). O estágio de inundação em Woodbury é de 10 m (Flood stage at Woodbury is 33 feet). Pequenas inundações ocorrem nesse nível, com terras planas em torno da cidade de Woodbury cobertas pelas águas (Minor flooding occurs at this level, with some lowlands around the town of Woodbury covered with water).|
|The Ohio River em Tell City com altura máxima de 11, 8 m por volta das 0700EST em 18 de dezembro (The Ohio River at Tell City crested at 39.0 feet around 7 AM EST on December 18). O estágio de inundação em Tell City é de 11,5 m (Flood stage at Tell City is 38 feet). Nesse nível, o rio começa a transbordar nas margens acima da escala (At this level, the river begins to overflow its banks above the gage). Inundações em Indiana Highway 66 entre Rome e Derby (Indiana Highway 66 floods between Rome and Derby).|

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


### operador project

    T | project cost=price*quantity, price

Selecione as colunas a serem incluídas, renomeadas ou removidas e insira novas colunas calculadas. A ordem das colunas no resultado é especificada pela ordem dos argumentos. Somente as colunas especificadas nos argumentos são incluídas no resultado: as demais colunas na entrada serão removidas. (Consulte também `extend`.)


**Sintaxe**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome de uma coluna que deve aparecer na saída. Se não houver uma *Expression*, uma coluna com esse nome deverá aparecer na entrada. [Nomes](#names) diferenciam maiúsculas de minúsculas e pode conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
* *Expression:* expressão escalar opcional que faz referência às colunas de entrada. 

    É válido retornar uma nova coluna calculada com o mesmo nome que uma coluna existente na entrada.

**Retorna**

Uma tabela que tem as colunas nomeadas como argumentos e a mesma quantidade de linhas da tabela de entrada.

**Exemplo**

O exemplo a seguir mostra vários tipos de manipulações que podem ser feitas usando o operador `project`. A tabela de entrada `T` tem três colunas de tipo `int`: `A`, `B` e `C`.

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### operador project-away

    T | project-away column1, column2, ...

Exclua as colunas especificadas. O resultado contém todas as colunas de entrada exceto aquelas que você nomear.

### operador range

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

Os argumentos devem ser valores numéricos, de data ou de período de tempo. Eles não podem referenciar as colunas de nenhuma tabela. (Se quiser calcular o intervalo com base em uma tabela de entrada, use a [função *range*](#range), talvez com o [operador mvexpand](#mvexpand-operator).)

**Retorna**

Uma tabela com uma única coluna chamada *ColumnName*, cujos valores são *Start*, *Start* + *Step*, ... até *Stop* inclusive.

**Exemplo**

```AIQL
range Steps from 1 to 8 step 3
```

Uma tabela com uma única coluna chamada `Steps` cujo tipo é `long` e cujos valores são `1`, `4` e `7`.

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

### operador reduce

    exceptions | reduce by outerMessage

Tenta agrupar registros semelhantes. Para cada grupo, o operador envia o `Pattern` que acredita que descreva melhor o grupo e o `Count` de registros nesse grupo.


![](./media/app-insights-analytics-reference/reduce.png)

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


### renderizar política

    T | render [ table | timechart  | barchart | piechart ]

A renderização instrui a camada de apresentação sobre como mostrar a tabela. Ela deve ser o último elemento do pipe. É uma alternativa conveniente ao uso dos controles de exibição, permitindo que você salve uma consulta com um método de apresentação específico.

### restringir cláusula 

Especifica o conjunto de nomes de tabela disponíveis para os operadores que seguem. Por exemplo:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### operador sort 

    T | sort by country asc, price desc

Classifica as linhas da tabela de entrada em ordem de acordo com uma ou mais colunas.

**Alias** `order`

**Sintaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* a tabela de entrada a ser classificada.
* *Column:* coluna de *T* de acordo com a qual a classificação deve ser feita. O tipo dos valores deve ser numérico, de data, hora ou cadeia de caracteres.
* `asc` Classificar em ordem crescente, do mais baixo para o mais alto. O padrão é `desc`, em ordem decrescente do mais alto para o mais baixo.

**Exemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas as linhas na tabela Traces que têm um `ActivityId` específico, classificadas por seu carimbo de data/hora.

### operador summarize

Produz uma tabela que agrega o conteúdo da tabela de entrada.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Uma tabela que mostra o número, a duração média da solicitação e o conjunto de cidades em cada país. Há uma linha na saída para cada país distinto. As colunas de saída mostram a contagem, a duração média, as cidades e o país. Todas as outras colunas de entrada são ignoradas.


    T | summarize count() by price_range=bin(price, 10.0)

Uma tabela que mostra quantos itens têm preços em cada intervalo [0,10,0], [10,0,20,0] e assim por diante. Este exemplo tem uma coluna para a contagem e um para a faixa de preços. Todas as outras colunas de entrada são ignoradas.


**Sintaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Column:* nome opcional para uma coluna de resultados. Assume o padrão de um nome derivado da expressão. [Nomes](#names) diferenciam maiúsculas de minúsculas e pode conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
* *Aggregation:* uma chamada para uma função de agregação, como `count()` ou `avg()`, com nomes de coluna como argumentos. Consulte [agregações](#aggregations).
* *GroupExpression:* uma expressão sobre as colunas que fornece um conjunto de valores distintos. Normalmente, é um nome de coluna que já fornece um conjunto restrito de valores ou `bin()` com uma coluna numérica ou de hora como argumento. 

Se você fornecer uma expressão numérica ou de hora sem usar `bin()`, a Análise a aplicará automaticamente com um intervalo de `1h` para horas ou de `1.0` para números.

Se você não fornecer um *GroupExpression*, toda a tabela será resumida em uma única linha de saída.



**Retorna**

As linhas de entrada são organizadas em grupos com os mesmos valores das expressões `by`. Em seguida, as funções de agregação especificadas são calculadas sobre cada grupo, produzindo uma linha para cada grupo. O resultado contém as colunas `by` e, também, pelo menos uma coluna para cada agregação calculada. (Algumas funções de agregação retornam várias colunas.)

O resultado tem a mesma quantidade de linhas das diferentes combinações de valores `by`. Se quiser resumir intervalos de valores numéricos, use `bin()` para reduzir os intervalos a valores distintos.

**Observação**

Embora você possa fornecer expressões aleatórias para as expressões de agregação e de agrupamento, é mais eficiente usar nomes de coluna simples ou aplicar `bin()` a uma coluna numérica.



### operador take

Alias de [limit](#limit-operator)


### operador top

    T | top 5 by Name desc

Retorna os primeiros registros *N* classificados pelas colunas especificadas.


**Sintaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argumentos**

* *NumberOfRows:* o número de linhas de *T* a serem retornadas.
* *Sort\_expression:* uma expressão de acordo com a qual as linhas devem ser classificadas. Normalmente é apenas um nome de coluna. Você pode especificar mais de um sort\_expression.
* `asc` ou `desc` (o padrão) pode surgir para controlar se a seleção é realmente da parte "inferior" ou "superior" do intervalo.


**Dicas**

`top 5 by name` é superficialmente equivale a `sort by name | take 5`. No entanto, é executado mais depressa e sempre retorna resultados classificados, enquanto `take` não oferece essa garantia.


### operador union

     Table1 | union Table2, Table3

Usa duas ou mais tabelas e retorna as linhas de todas elas.

**Sintaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Table1*, *Table2* ...
 *  O nome de uma tabela, como `requests`, ou uma tabela definida em uma [cláusula let](#let-clause); ou
 *  Uma expressão de consulta, como `(requests | where success=="True")`
 *  Um conjunto de tabelas especificadas com um curinga. Por exemplo, `e*` pode formar a união de todas as tabelas definidas nas cláusulas let anteriores cujo nome começa com “e”, juntamente com a tabela “exceções”.
* `kind`: 
 * `inner`: o resultado tem o subconjunto de colunas que são comuns a todas as tabelas de entrada.
 * `outer`: o resultado tem todas as colunas que ocorrem em qualquer uma das entradas. As células que não foram definidas por uma linha de entrada são definidas como `null`.
* `withsource=`*ColumnName:* se especificado, a saída inclui uma coluna chamada *ColumnName* cujo valor indica qual tabela de origem contribuiu com cada linha.

**Retorna**

Uma tabela com tantas linhas quanto houver em todas as tabelas de entrada e tantas colunas quantos nomes de coluna exclusivos constarem nas entradas.

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

### operador where

     T | where fruit=="apple"

Filtra uma tabela para o subconjunto de linhas que satisfazem a um predicado.

**Alias** `filter`

**Sintaxe**

    T | where Predicate

**Argumentos**

* *T:* a entrada de tabela cujos registros devem ser filtrados.
* *Predicate:* uma [expressão](#boolean) `boolean` nas colunas de *T*. É avaliado para cada linha em *T*.

**Retorna**

As linhas em *T* para as quais o *Predicate* é `true`.

**Dicas**

Para obter o desempenho mais rápido:

* **Use comparações simples** entre os nomes de coluna e as constantes. (“Constante” significa constante ao longo da tabela. Portanto, `now()` e `ago()` estão OK, bem como valores escalares atribuídos usando uma [cláusula `let`](#let-clause)).

    Por exemplo, prefira `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Termos mais simples primeiro**: se houver várias cláusulas unidas com `and`, primeiro coloque as cláusulas que envolvem apenas uma coluna. Assim, `Timestamp > ago(1d) and OpId == EventId` é melhor do que o oposto.


**Exemplo**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros que existem há menos de uma hora e são provenientes da Origem chamada "Kuskus" e têm duas colunas de mesmo valor.

Observe que colocamos a comparação entre duas colunas por último, pois ela não pode utilizar o índice e força uma verificação.



## Agregações

As agregações são funções usadas para combinar valores em grupos criados em [resumir operação](#summarize-operator). Por exemplo, nesta consulta, dcount() é uma função de agregação:

    requests | summarize dcount(name) by success

### qualquer 

    any(Expression)

Seleciona aleatoriamente uma linha do grupo e retorna o valor da expressão especificada.

Isso é útil, por exemplo, quando alguma coluna tiver uma quantidade grande de valores semelhantes (por exemplo, uma coluna de "texto de erro") e você quiser realizar uma amostragem única dessa coluna de acordo com um valor exclusivo da chave de grupo composta.

**Exemplo**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
### argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Localiza uma linha no grupo que minimiza/maximiza *ExprToMaximize* e retorna o valor de *ExprToReturn* (ou `*` para retornar a linha inteira).

**Dica**: as colunas passadas são automaticamente renomeadas. Para ter certeza de que você está usando os nomes corretos, inspecione os resultados usando `take 5` antes de canalizar os resultados para outro operador.

**Exemplos**

Para cada nome de solicitação, mostre quando ocorreu a solicitação mais longa:

    requests | summarize argmax(duration, timestamp) by name

Mostre todos os detalhes da solicitação mais longa, não apenas o carimbo de hora:

    requests | summarize argmax(duration, *) by name


Encontre o menor valor de cada métrica, junto com seu carimbo de hora e outros dados:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### avg

    avg(Expression)

Calcula a média da *Expression* no grupo.

### buildschema

    buildschema(DynamicExpression)

Retorna o esquema mínimo que admite todos os valores de *DynamicExpression*.

O tipo de coluna do parâmetro deve ser `dynamic`, uma matriz ou recipiente de propriedades.

**Exemplo**

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

**Exemplo**

Suponha que a coluna de entrada tenha três valores dinâmicos:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


O esquema resultante seria:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

O esquema nos informa que:

* O objeto raiz é um contêiner com quatro propriedades chamadas x, y, z e t.
* A propriedade chamada "x", que pode ser do tipo "int" ou do tipo "string".
* A propriedade chamada "y", que pode ser do tipo "double" ou outro contêiner com uma propriedade chamada "w" do tipo "string".
* A palavra-chave ``indexer`` indica que "z" e "t" são matrizes.
* Cada item na matriz "z" é um int ou string.
* "t" é uma matriz de cadeias de caracteres.
* Todas as propriedades são implicitamente opcionais e qualquer matriz pode estar vazia.

##### Modelo de esquema

A sintaxe do esquema retornado é:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

São equivalentes a um subconjunto das anotações do tipo TypeScript, codificadas como um valor dinâmico. No Typescript, o exemplo de esquema seria:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### count

    count([ Predicate ])

Retorna uma contagem de linhas para a qual *Predicate* é avaliado como `true`. Se nenhum *Predicate* for especificado, retornará o número total de registros no grupo.

**Dica de desempenho**: use `summarize count(filter)` em vez de `where filter | summarize count()`

> [AZURE.NOTE] Evite usar count() para localizar o número de solicitações, exceções ou outros eventos que ocorreram. Quando a [amostragem](app-insights-sampling.md) está em operação, o número de pontos de dados é menor que o número de eventos reais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantido.

### countif

    countif(Predicate)

Retorna uma contagem de linhas para a qual *Predicate* é avaliado como `true`.

**Dica de desempenho**: use `summarize countif(filter)` em vez de `where filter | summarize count()`

> [AZURE.NOTE] Evite usar countif() para localizar o número de solicitações, exceções ou outros eventos que ocorreram. Quando a [amostragem](app-insights-sampling.md) está em operação, o número de pontos de dados é menor que o número de eventos reais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantido.

### dcount

    dcount( Expression [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* no grupo. (Para listar os valores distintos, use [`makeset`](#makeset)).

*Accuracy*, se for especificada, controlará o equilíbrio entre velocidade e precisão.

 * `0` = o cálculo menos preciso e mais rápido.
 * `1`, que é o padrão, equilibra a precisão e o tempo de cálculo; cerca de 0,8% de erro.
 * `2` = cálculo mais preciso e mais lento; cerca de 0,4% de erro.

**Exemplo**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### makelist

    makelist(Expr [ ,  MaxListSize ] )

Retorna uma matriz `dynamic` (JSON) de todos os valores de *Expr* no grupo.

* *MaxListSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é de *128*).

### makeset

    makeset(Expression [ , MaxSetSize ] )

Retorna uma matriz `dynamic` (JSON) do conjunto de valores distintos que *Expr* usa no grupo. (Dica: para contar apenas os valores distintos, use [`dcount`](#dcount)).
  
*  *MaxSetSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é de *128*).

**Exemplo**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Consulte também o [`mvexpand` operador](#mvexpand-operator) para a função oposta.


### max, min

    max(Expr)

Calcula o número máximo de *Expr*.
    
    min(Expr)

Calcula o número mínimo de *Expr*.

**Dica**: isso apresenta o mínimo ou o máximo por conta própria, por exemplo, o preço mais alto ou o mais baixo. No entanto, se você quiser outras colunas na linha, por exemplo, o nome do fornecedor com o menor preço, use [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
### percentile, percentiles

    percentile(Expression, Percentile)

Retorna uma estimativa para a *Expression* do percentual especificado no grupo. A precisão depende da densidade da população na região do percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Como `percentile()`, mas calcula um número de valores de percentil (que é mais rápido do que calcular cada percentil individualmente).

**Exemplos**


O valor de `duration`, que é maior do que 95% do conjunto de exemplo, e menor do que 5% do conjunto de exemplo, calculado para cada nome de solicitação:

    request 
    | summarize percentile(duration, 95)
      by name

Omita "by..." para calcular toda a tabela.

Calcule simultaneamente vários percentuais para nomes de solicitação diferentes:

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Os resultados mostram que para a solicitação /Events/Index, 5% das solicitações recebem respostas em menos de 2,44 s, metade delas em 3,52 s, e 5% são mais lentas do que 6,85 s.


Calcule várias estatísticas:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

##### Erro de estimativa em percentuais

A agregação de percentis fornece um valor aproximado usando [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Alguns pontos importantes:

* Os limites no erro de estimativa variam de acordo percentil solicitado. A maior precisão está nas extremidades da escala de [0 a 100], os percentuais 0 e 100 são os valores mínimo e máximo exatos da distribuição. A precisão diminui gradativamente rumo à parte central da escala. Ela atinge seu pior valor na mediana e está limitada a 1%. 
* Os limites de erro são observados na classificação, não no valor. Suponha que percentil (X, 50) retornou o valor de Xm. A estimativa garante que pelo menos 49% e, no máximo, 51% dos valores de X sejam inferiores Xm. Não há qualquer limite teórico sobre a diferença entre Xm e o valor real da mediana de X.

### stdev

     stdev(Expr)

Retorna o desvio padrão de *Expr* no grupo.

### variance

    variance(Expr)

Retorna a variação de *Expr* no grupo.

### sum

    sum(Expr)

Retorna a soma de *Expr* no grupo.


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
* *ifTrue:* uma expressão que será avaliada e terá seu valor retornado da função se *predicate* for avaliado como `true`.
* *ifFalse:* uma expressão que será avaliada e terá seu valor retornado da função se *predicate* for avaliado como `false`.

**Retorna**

Essa função retornará o valor de *ifTrue* se *predicate* for avaliado como `true` ou retornará o valor de *ifFalse* se ocorrer o contrário.

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
| - | Subtrair | 
| * | Multiplicar | 
| / | Dividir | 
| % | Módulo | 
|| 
|`<` |Menor 
|`<=`|Menor ou Igual a 
|`>` |Maior 
|`>=`|Maior ou Igual a 
|`<>`|Diferente de 
|`!=`|Diferente de




### bin

Arredonda os valores até um número inteiro múltiplo de um determinado tamanho de compartimentalização. Muito usado na consulta [`summarize by`](#summarize-operator). Se você tiver um conjunto disperso de valores, eles serão agrupados em um conjunto menor de valores específicos.

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
### exp

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v



### floor

Um alias para [`bin()`](#bin).


### log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` deve ser um número real > 0. Caso contrário, nulo será retornado.

### rand

Um gerador de número aleatório.

* `rand()`: um número real entre 0,0 e 1,0
* `rand(n)`: um número inteiro entre 0 e n-1




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

A barra invertida (``) é usada para caracteres de escape como `\t` (guia), `\n` (nova linha) e instâncias do caractere de aspas.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Literais de cadeia de caracteres ofuscados

Literais de cadeia de caracteres ofuscados são cadeias de caracteres que serão obscurecidas pela Análise quando ela produzir a saída da cadeia de caracteres (por exemplo, durante o rastreamento). O processo de ofuscação substitui todos os caracteres ofuscados por um caractere de início (`*`).

Para formar um literal de cadeia de caracteres ofuscado, preceda `h` ou “H”. Por exemplo:

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
* *search:* a cadeia de caracteres simples ou a expressão regular a ser correspondida em *text*.
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

Obtém uma correspondência para uma [expressão regular](#regular-expressions) por meio de uma cadeia de caracteres de texto. Opcionalmente, converte a subcadeia de caracteres extraída para o tipo indicado.

**Sintaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* uma [expressão regular](#regular-expressions).
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

True se o argumento for uma cadeia de caracteres vazia ou nula. Consulte também [isnull](#isnull).


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

* *source:* a cadeia de caracteres de origem por meio da qual a subcadeia de caracteres será extraída.
* *startingIndex:* a posição do caractere inicial com base em zero da subcadeia de caracteres solicitada.
* *length:* um parâmetro opcional que pode ser usado para especificar o número solicitado de caracteres na subcadeia de caracteres. 

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


## Matrizes, objetos e dinâmico

[literais](#dynamic-literals) | [conversão](#casting-dynamic-objects) | [operadores](#operators) | [cláusulas let](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Este é o resultado de uma consulta em uma exceção do Application Insights. O valor em `details` é uma matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indexing:** índice de matrizes e objetos, assim como no JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Contudo, use `arraylength` e outras funções da Análise (não “.length”).

**Casting**: em alguns casos, é necessário converter um elemento que você extraiu de um objeto, pois seu tipo pode variar. Por exemplo, `summarize...to` precisa de um tipo específico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literals**: para criar uma matriz explícita ou um objeto de recipiente de propriedades, escreva-a como uma cadeia de caracteres JSON e a converta:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** para extrair e separar as propriedades de um objeto em linhas separadas, use mvexpand:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** para localizar todos os caminhos em um objeto complexo:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

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

* `parsejson('[43, 21, 65]')`: uma matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`: um único valor de tipo dinâmico contendo um número
* `parsejson('"21"')`: um único valor de tipo dinâmico contendo uma cadeia de caracteres

Observe que, ao contrário do JavaScript, o JSON exige o uso de aspas duplas (`"`) ao redor de cadeias de caracteres. Portanto, é geralmente mais fácil citar literais de uma cadeia de caracteres codificada em JSON usando aspas simples (`'`).

Este exemplo cria um valor dinâmico e então usa seus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


## Funções de objeto dinâmico

|||
|---|---|
| *value* `in` *array*| True se houver um elemento de *array* que = = *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True se não houver um elemento de *array* que = = *value*
|[`arraylength(`array`)`](#arraylength)| Null se não for uma matriz
|[`extractjson(`path,object`)`](#extractjson)|Usa o caminho para navegar no objeto.
|[`parsejson(`source`)`](#parsejson)| Transforma uma cadeia de caracteres JSON em um objeto dinâmico.
|[`range(`from,to,step`)`](#range)| Uma matriz de valores
|[`mvexpand` listColumn](#mvexpand-operator) | Replica uma linha para cada valor em uma lista em uma célula especificada.
|[`summarize buildschema(`column`)`](#buildschema) |Infere o esquema de tipo a partir do conteúdo da coluna
|[`summarize makelist(`column`)` ](#makelist)| Mescla os grupos de linhas e coloca os valores da coluna em uma matriz.
|[`summarize makeset(`column`)`](#makeset) | Mescla os grupos de linhas e coloca os valores da coluna em uma matriz, sem duplicação.

### Objetos dinâmicos em cláusulas let


As [cláusulas let](#let-clause) armazenam valores dinâmicos como cadeias de caracteres, portanto, essas duas cláusulas são equivalentes, e ambas precisam de `parsejson` (ou `todynamic`) antes de serem usadas:

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



**Dicas de desempenho**

* Aplicar cláusulas where antes de usar `extractjson()`
* Considere o uso de uma correspondência da expressão regular com [extract](#extract). Isso pode ser executado muito mais rápido, e será eficaz se JSON for produzido a partir de um modelo.
* Use `parsejson()` se você precisar extrair mais de um valor de JSON.
* Considere analisar o JSON na ingestão declarando o tipo da coluna como dinâmica.

### Expressões de caminho JSON

|||
|---|---|
|`$`|Objeto raiz|
|`@`|Objeto atual|
|`[0]`|Subscrito de matriz|
|`.` ou `[0]` | Filho|

*(No momento, não implementamos caracteres curinga, recursão, união ou fatias.)*




### parsejson

Interpreta um `string` como um [valor de JSON](http://json.org/) e retorna o valor como `dynamic`. É superior ao uso de `extractjson()` quando você precisa extrair mais de um elemento de um objeto JSON composto.

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

daí, o fragmento a seguir recupera o valor do slot `duration` no objeto e, a por meio disso, recupera dois slots, `duration.value` e `duration.min` (`118.0` e `110.0`, respectivamente).

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

## Nomes

Os nomes podem ter até 1.024 caracteres. Eles diferenciam maiúsculas de minúsculas e podem conter letras, dígitos e sublinhados (`_`).

Citeu m nome usando ['... '] ou [" ... "] para incluir outros caracteres ou usar uma palavra-chave como um nome. Por exemplo:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|['path\\file\\n'x''] | Use \\ para caracteres de escape|
|["d-e.=/f#\\n"] | |
|[@'path\\file'] | Sem escapes – \\ é literal|
|[@"\\now & then"] | |
|[where] | Usando uma palavra-chave de linguagem como um nome|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0518_2016-->