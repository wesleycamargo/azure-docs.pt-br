<properties 
	pageTitle="Referência do Analytics no Application Insights | Microsoft Azure" 
	description="Referência de instruções na Análise, a ferramenta de pesquisa avançada do Application Insights. " 
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
	ms.date="06/07/2016" 
	ms.author="awills"/>

# Referência da Análise

[Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.


## Índice


**Let e set** [let](#let-clause) | [set](#set-clause)


**Consultas e operadores** [count](#count-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [join](#join-operator) | [limit](#limit-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sort](#sort-operator) | [summarize](#summarize-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator)

**Agregações** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**Escalares** [Literais Boolianos](#boolean-literals) | [Operadores boolianos](#boolean-operators) | [Conversões](#casts) | [Comparações escalares](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Números** [Operadores aritméticos](#arithmetic-operators) | [Literais numéricos](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Data e hora** [Expressões de data e hora](#date-and-time-expressions) | [Literais de data e hora](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Cadeia de caracteres** [GUIDs](#guids) | [Literais de cadeias de caracteres ocultos](#obfuscated-string-literals) | [Literais de cadeias de caracteres](#string-literals) | [Comparações de cadeia de caracteres](#string-comparisons) | [countof](#countof) | [extract](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrizes, objetos e dinâmica** [Literais de matriz e objeto](#array-and-object-literals) | [Funções de objeto dinâmico](#dynamic-object-functions) | [Objetos dinâmicos em cláusulas let](#dynamic-objects-in-let-clauses) | [Expressões de caminho JSON](#json-path-expressions) | [Nomes](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## Let e set

### cláusula Let

**Let tabular - nomeando uma tabela**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Let escalar - nomeando um valor**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Let lambda - nomeando uma função**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime){strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Uma cláusula let associa um [nome](#names) a um resultado tabular, um valor escalar ou uma função. A cláusula é um prefixo para uma consulta e o escopo da associação é essa consulta. (Let não fornece uma maneira de nomear itens que você usa mais tarde na sessão.)

**Sintaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
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

### Cláusula Set

A cláusula set define uma opção para a duração da consulta. As opções de consulta controlam como uma consulta será executada e como retornará resultados. Elas podem ser sinalizadores boolianos (desativado por padrão) ou ter algum valor inteiro. Uma consulta pode conter zero, uma ou mais instruções set. As instruções Set afetam somente as instruções de expressão tabular que as rastreiam na ordem do programa.

    set OptionName [= OptionValue] ; query


|Nome | Implicação se definido como verdadeiro
|---|---
|querytrace| Aumenta o nível de rastreamentos de depuração gerados por uma consulta. 
|noexecute| Desabilita a execução real da consulta (apenas a fase de planejamento da consulta é executada). 
|perftrace| Habilita o rastreamento de desempenho. 
|notruncation| Desabilita o truncamento do conjunto de resultados. 
|truncationmaxsize| Limita o tamanho de dados de resultado de consulta (em Bytes). 
|truncationmaxrecords| Limita o número de registros de resultado de consulta. 
|nostreaming |Desabilita o streaming do conjunto de resultados. 

**Exemplo**

```

    set querytrace;
    requests | take 100
```

## Consultas e operadores

Uma consulta sobre a telemetria é composta de uma referência a um fluxo de origem, seguida de um pipeline de filtros. Por exemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro prefixado pelo caractere de barra vertical `|` é uma instância de um *operador* com alguns parâmetros. A entrada do operador é a tabela que é o resultado do pipeline anterior. Na maioria dos casos, os parâmetros são [expressões escalares](#scalars) sobre as colunas da entrada. Em alguns casos, os parâmetros são os nomes das colunas da entrada e, em outros casos, o parâmetro é uma segunda tabela. O resultado de uma consulta é sempre uma tabela, mesmo que ela tenha somente uma coluna e uma linha.

Consultas podem conter quebras de linha simples, mas são finalizadas por uma linha em branco. Elas podem conter comentários entre `//` e o fim da linha.

Uma consulta pode ser prefixada por uma ou mais [cláusulas let](#let-clause), que definem escalares, tabelas ou funções que podem ser usados na consulta.

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

* *T*: os dados tabulares cujos registros serão contados.

**Retorna**

Essa função retorna uma tabela com um único registro e uma coluna do tipo `long`. O valor da célula única é o número de registros em *T*.

**Exemplo**

```AIQL
requests | count
```

### operador evaluate

`evaluate` é um mecanismo de extensão que permite que algoritmos especializados sejam anexados a consultas.

`evaluate` deve ser o último operador no pipeline de consulta (exceto para uma possível `render`). Ele não pode aparecer no corpo da função.

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basket) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### evaluate autocluster

     T | evaluate autocluster()

O AutoCluster encontra padrões comuns de atributos discretos (dimensões) nos dados e reduzirá os resultados da consulta original (se ela tiver 100 ou 100 mil linhas) para um número pequeno de padrões. O AutoCluster foi desenvolvido para ajudar a analisar falhas (por exemplo, exceções, panes) mas potencialmente pode funcionar em qualquer conjunto de dados filtrado.

**Sintaxe**

    T | evaluate autocluster( arguments )

**Retorna**

O AutoCluster retorna um conjunto (geralmente pequeno) de padrões que capturam as partes dos dados com valores comuns compartilhados entre vários atributos discretos. Cada padrão é representado por uma linha nos resultados.

As duas primeiras colunas são a contagem e o percentual de linhas da consulta original capturadas pelo padrão. As colunas restantes são da consulta original e seu valor é um valor específico da coluna ou '*', que significa valores de variáveis.

Observe que os padrões não são separados: eles podem ser sobrepostos e geralmente não abrangem todas as linhas originais. Nem todas as linhas podem ficar em qualquer padrão.

**Dicas**

* Use `where` e `project` no pipe de entrada para reduzir os dados apenas aos em que você está interessado.
* Quando você encontrar uma linha interessante, talvez queira se aprofundar ainda mais adicionando seus valores específicos ao filtro `where`.

**Argumentos (todos opcionais)**

* `output=all | values | minimal`

    O formato dos resultados. As colunas Count e Percent sempre aparecem nos resultados.

 * `all` - todas as colunas de entrada são de saída
 * `values` - filtra colunas que só tenham '*' nos resultados
 * `minimal` -também filtra as colunas idênticas para todas as linhas na consulta original.


* `min_percent=`*double* (padrão: 1)

    A cobertura de percentual mínimo das linhas geradas.

    Exemplo: `T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=` *int* (padrão: 25)

    O número de sementes determina o número de pontos de pesquisa local inicial do algoritmo. Em alguns casos, dependendo da estrutura dos dados, o aumento do número de sementes aumenta o número (ou a qualidade) dos resultados por meio de um espaço de pesquisa maior em uma consulta mais lenta. O argumento num\_seeds tem menos resultados em ambas as direções e,portanto, reduzi-lo para menos de cinco atingirá melhorias de desempenho imperceptíveis e aumentá-lo para mais de 50 raramente gerará padrões adicionais.

    Exemplo: `T | evaluate autocluster("num_seeds=50")`


* `size_weight=` *0<double<1*+ (padrão: 0,5)

    Oferece algum controle sobre o equilíbrio entre genéricos (alta cobertura) e informativos (muitos valores compartilhados). O aumento de size\_weight normalmente reduz o número de padrões, e cada padrão tende a cobrir um percentual maior. A redução de size\_weight geralmente produz padrões mais específicos com mais valores compartilhados e uma cobertura de percentual menor. A fórmula por trás disso é uma média geométrica ponderada entre a pontuação genérica normalizada e a pontuação informativa com size\_weight e 1-size\_weight como os pesos.

    Exemplo: `T | evaluate autocluster("size_weight=0.8")`


* `weight_column=` *column\_name*

    Considera cada linha na entrada de acordo com o peso especificado (por padrão, cada linha tem um peso '1'); o uso comum de uma coluna de peso é levar em consideração a amostragem de conta ou segmentação/agregação dos dados já incorporados a cada linha.

    Exemplo: `T | evaluate autocluster("weight_column=sample_Count")`



#### evaluate basket

     T | evaluate basket()

A cesta encontra todos os padrões frequentes de atributos discretos (dimensões) nos dados e retornará todos os padrões frequentes que passaram do limite de frequência na consulta original. A cesta certamente encontrará todos os padrões frequentes nos dados, mas o tempo de execução polinomial não é garantido. O tempo de execução da consulta é linear no número de linhas, mas em alguns casos pode ser exponencial no número de colunas (dimensões). A cesta se baseia no algoritmo Apriori, originalmente desenvolvido para mineração de dados de análise da cesta.

**Retorna**

Todos os padrões que aparecem em mais do que uma fração especificada (padrão de 0,05) dos eventos.

**Argumentos (todos opcionais)**


* `threshold=` *0,015<double<1* (padrão: 0,05)

    Define a proporção mínima de linhas a ser considerado como frequente (os padrões com uma proporção menor não serão retornados).

    Exemplo: `T | evaluate basket("threshold=0.02")`


* `weight_column=` *column\_name*

    Considera cada linha na entrada de acordo com o peso especificado (por padrão, cada linha tem um peso '1'); o uso comum de uma coluna de peso é levar em consideração a amostragem de conta ou segmentação/agregação dos dados já incorporados a cada linha.

    Exemplo: T | evaluate basket("weight\_column=sample\_Count")


* `max_dims=` *1<int* (padrão: 5)

    Define o número máximo de dimensões não correlacionadas por cesta, limitado por padrão para diminuir o tempo de execução de consulta.


* `output=minimize` | `all`

    O formato dos resultados. As colunas Count e Percent sempre aparecem nos resultados.

 * `minimize` - filtra colunas que só tenham '*' nos resultados.
 * `all` - todas as colunas de entrada são de saída.




#### evaluate diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compara dois conjuntos de dados da mesma estrutura e localiza os padrões de atributos discretos (dimensões) que caracterizam as diferenças entre os dois conjuntos de dados. Diffpatterns foi desenvolvido para ajudar a analisar falhas (por exemplo, ao comparar falhas a não falhas em um determinado período de tempo), mas pode encontrar diferenças entre quaisquer dois conjuntos de dados da mesma estrutura.

**Sintaxe**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**Retorna**

Diffpatterns retorna um conjunto de padrões (geralmente pequeno) que capturam diferentes partes dos dados em conjuntos de dois (ou seja, um padrão que captura um percentual alto das linhas no primeiro conjunto de dados e um percentual baixo das linhas do segundo conjunto). Cada padrão é representado por uma linha nos resultados.

As quatro primeiras colunas são a contagem e o percentual de linhas da consulta original capturadas pelo padrão em cada conjunto, a quinta coluna é a diferença (em pontos absolutos de percentual) entre os dois conjuntos. As colunas restantes são da consulta original e seu valor é um valor específico da coluna ou *, que significa valores de variáveis.

Observe que os padrões não são diferentes: eles podem ser sobrepostos e geralmente não abrangem todas as linhas originais. Nem todas as linhas podem ficar em qualquer padrão.

**Dicas**

* Use where e project no pipe de entrada para reduzir os dados apenas aos em que você está interessado.

* Quando você encontrar uma linha interessante, talvez queira se aprofundar ainda mais adicionando seus valores específicos ao filtro where.

**Argumentos**

* `split=` *nome da coluna* (obrigatório)

    A coluna deve ter exatamente dois valores. Se necessário, crie uma coluna deste tipo:

    `requests | extend fault = toint(resultCode) >= 500` <br/> `| evaluate diffpatterns("split=fault")`

* `target=` *string*

    Informa ao algoritmo para procurar apenas os padrões que tenham um percentual mais alto no conjunto de dados de destino, o destino deve ser um dos dois valores da coluna de divisão.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=` *0,015<double<1* (padrão: 0,05)

    Define a diferença mínima padrão (proporção) entre os dois conjuntos.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    O formato dos resultados. As colunas Count e Percent sempre aparecem nos resultados.

 * `minimize` - filtra colunas que só tenham '*' nos resultados
 * `all` - todas as colunas de entrada são de saída

* `weight_column=` *column\_name*

    Considera cada linha na entrada de acordo com o peso especificado (por padrão, cada linha tem um peso '1'). Um uso comum de uma coluna de peso é para amostragem de conta ou agregação/segmentação de dados já incorporados a cada linha.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### evaluate extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns é usado para enriquecer uma tabela com várias colunas simples dinamicamente extraídas de colunas (semi)estruturadas com base em seu tipo. No momento, oferece suporte somente a colunas json, serialização dinâmica e de cadeia de caracteres de jsons.


* `max_columns=` *int* (padrão: 10)

    O número de novas colunas adicionadas é dinâmico e pode ser muito grande (na verdade, é o número de chaves diferentes em todos os registros json) e, portanto, devemos limitá-lo. As novas colunas são classificadas em ordem decrescente com base na sua frequência e até max\_columns são adicionadas à tabela.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*double* (padrão: 10,0)

    Outra maneira de limitar novas colunas ignorando colunas cuja frequência seja menor do que min\_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=` *bool* (padrão: true)

    Se true, o nome da coluna complexa será adicionado como um prefixo aos nomes de colunas extraídos.


* `prefix_delimiter=` *string* (padrão: "\_")

    Se add\_prefix=true, esse parâmetro definirá o delimitador que será usado para concatenar os nomes das novas colunas.

    `T | evaluate extractcolumns("json_column_name=json",` <br/> `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=` *bool* (padrão: false)

    Se true, as colunas (json) originais serão mantidas na tabela de saída.


* `output=query | table`

    O formato dos resultados.

 * `table` - a saída é a mesma tabela como recebida menos as colunas de entrada especificadas mais novas colunas que foram extraídas de colunas de entrada.
 * `query` -a saída é uma cadeia de caracteres que representa a consulta que você faria para obter o resultado como tabela.




### operador extend

     T | extend duration = stopTime - startTime

Acrescente uma ou mais colunas calculadas a uma tabela.


**Sintaxe**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome das colunas a serem adicionadas. [Names](#names) diferenciam maiúsculas de minúsculas e podem conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
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

* *Tabela1*: o ‘lado esquerdo’ da junção.
* *Tabela2*: o ‘lado direito’ da junção. Pode ser uma expressão de consulta aninhada que gera uma tabela.
* *CommonColumn*: uma coluna que tem o mesmo nome nas duas tabelas.
* *Variante*: especifica como deve ser feita a correspondência entre as linhas das duas tabelas.

**Retorna**

Uma tabela com:

* Uma coluna para cada coluna em cada uma das duas tabelas, incluindo as chaves correspondentes. As colunas do lado direito serão automaticamente renomeadas se houver conflitos de nome.
* Uma linha para cada correspondência entre as tabelas de entrada. Uma correspondência é uma linha selecionada de uma tabela que tem o mesmo valor para todos os campos `on` que uma linha da outra tabela.

* `Kind` não especificado

    Apenas uma linha do lado esquerdo é correspondida para cada valor da chave `on`. A saída contém uma linha para cada correspondência dessa linha com linhas da direita.

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


### operador limit

     T | limit 5

Retorna até o número especificado de linhas da tabela de entrada. Não há garantia de quais registros serão retornados. (Para retornar registros específicos, use [`top`](#top-operator)).

**Alias** `take`

**Sintaxe**

    T | limit NumberOfRows


**Dicas**

`Take` é uma maneira simples e eficiente de ver um exemplo dos resultados quando você trabalha de forma interativa. Lembre-se de que não há garantia quanto à produção de linhas específicas ou de sua produção em uma ordem específica.

Há um limite implícito quanto ao número de linhas retornadas ao cliente, mesmo que você não use `take`. Para aumentar o limite, use a opção de solicitação do cliente `notruncation`.



### operador mvexpand

    T | mvexpand listColumn 

Expande uma lista de uma célula dinamicamente tipada (JSON) para que cada entrada tenha uma linha separada. Todas as outras células em uma linha expandida são duplicadas.

(Veja também [`summarize makelist`](#summarize-operator), que executa a função oposta).

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
* `bagexpansion=array`: os recipientes de propriedades são expandidos como estruturas de matriz de `[`*chave*`,`*valor*`]` de dois elementos, permitindo o acesso uniforme a chaves e valores (bem como, por exemplo, a execução de uma agregação de contagem distinta sobre nomes de propriedades).

**Exemplos**


    exceptions | take 1 
    | mvexpand details[0]

Divide um registro de exceção em linhas para cada item no campo de detalhes.



### operador parse

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrai valores de uma cadeia de caracteres. Pode usar a correspondência de expressões regulares ou simples.

**Sintaxe**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumentos**

* `T`: a tabela de entrada.
* `kind`:
 * `simple` (padrão): as cadeias de caracteres `Match` são cadeias de caracteres simples.
 * `relaxed`: se o texto não é analisado como o tipo de uma coluna, a coluna é definida como nula e a análise continua
 * `regex`: as cadeias de caracteres `Match` são expressões regulares.
* `Text`: uma coluna ou outra expressão que é avaliada ou pode ser convertida em uma cadeia de caracteres.
* *Match:* fazer a correspondência com a próxima parte da cadeia de caracteres e descartá-la.
* *Column:* atribuir a próxima parte da cadeia de caracteres a esta coluna. A coluna será criada se ainda não existir.
* *Type:* analisar a próxima parte da cadeia de caracteres como o tipo especificado, como int, date ou double.


**Retorna**

A tabela de entrada, estendida de acordo com a lista de Colunas.

Os elementos na cláusula `with` são comparados ao texto de origem sucessivamente. Cada elemento remove uma parte do texto de origem:

* Uma cadeia de caracteres literal ou uma expressão regular move o cursor correspondente pelo comprimento da correspondência.
* Em uma análise de regex, uma expressão regular pode usar o operador de minimização '?' para passar assim que possível para a correspondência seguinte.
* Um nome de coluna com um tipo analisa o texto como o tipo especificado. A menos que kind=relaxed, uma análise malsucedida invalida a correspondência do padrão inteiro.
* Um nome de coluna sem um tipo ou com o tipo 'string' copia o número mínimo de caracteres para obter a correspondência seguinte.
* ' * ' Ignora o número mínimo de caracteres para obter a correspondência seguinte. Você pode usar '*' no início e no final do padrão, ou depois de um tipo diferente de ‘string’ ou entre as correspondências de cadeia de caracteres.

Todos os elementos em um padrão de análise devem corresponder corretamente. Caso contrário, nenhum resultado será produzido. A exceção a essa regra é que, quando kind=relaxed, se uma análise de uma variável com tipo falhar, o restante da análise continuará.

**Exemplos**

*Simple:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | contador | presente | Ano
---|---|---|---
1 | 2 | socks | 63

*Relaxed:*

Quando a entrada contém uma correspondência correta para cada coluna com tipo, uma análise relaxed produz os mesmos resultados de uma análise simples. Mas se uma das colunas com tipo não for analisada corretamente, uma análise relaxed continuará a processar o restante do padrão, enquanto uma análise simples interromperá e falhará ao gerar quaisquer resultados.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x | presente | Ano
---|---|---
1 | socks | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\)"
| project-away x, s
```

recurso | fatia | lock | release | previous
---|---|---|---|---
Agendador | 16 | 02/17/2016 08:41:00 | 02/17/2016 08:41 | 2016-02-17T08:40:00Z

### operador project

    T | project cost=price*quantity, price

Selecione as colunas a serem incluídas, renomeadas ou removidas e insira novas colunas calculadas. A ordem das colunas no resultado é especificada pela ordem dos argumentos. Somente as colunas especificadas nos argumentos são incluídas no resultado: as demais colunas na entrada serão removidas. (Veja também `extend`).


**Sintaxe**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* a tabela de entrada.
* *ColumnName:* o nome de uma coluna que deve aparecer na saída. Se não houver uma *Expression*, uma coluna com esse nome deverá aparecer na entrada. [Names](#names) diferenciam maiúsculas de minúsculas e podem conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
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

Os argumentos devem ser valores numéricos, de data ou de período de tempo. Eles não podem referenciar as colunas de nenhuma tabela. (Se quiser calcular o intervalo com base em uma tabela de entrada, use a [função *range*](#range), talvez com o [operador mvexpand](#mvexpand-operator)).

**Retorna**

Uma tabela com uma única coluna chamada *ColumnName*, cujos valores são *Start*, *Start* + *Step*, ... até *Stop*, inclusive.

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

Mostra como o operador `range` pode ser usado para criar uma pequena tabela de dimensões ad-hoc, que será então usada para introduzir zeros quando os dados de origem não tiverem valores.

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
* *Column:* coluna de *T* de acordo com qual a classificação deve ser feita. O tipo dos valores deve ser numérico, de data, hora ou cadeia de caracteres.
* `asc` Classificar em ordem crescente, do mais baixo para o mais alto. O padrão é `desc`, em ordem decrescente, do mais alto para o mais baixo.

**Exemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas as linhas na tabela Traces com um `ActivityId` específico, classificadas por seu carimbo de data/hora.

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

* *Column:* nome opcional para uma coluna de resultados. Assume o padrão de um nome derivado da expressão. [Names](#names) diferenciam maiúsculas de minúsculas e podem conter caracteres alfabéticos, numéricos ou “\_”. Use `['...']` ou `["..."]` para citar palavras-chave ou nomes com outros caracteres.
* *Aggregation:* uma chamada para uma função de agregação, como `count()` ou `avg()`, com nomes de coluna como argumentos. Veja [agregações](#aggregations).
* *GroupExpression:* uma expressão sobre as colunas que fornece um conjunto de valores distintos. Normalmente, é um nome de coluna que já fornece um conjunto restrito de valores ou `bin()` com uma coluna numérica ou de hora como argumento.

Se você fornecer uma expressão numérica ou de hora sem usar `bin()`, o Analytics a aplicará automaticamente com um intervalo de `1h` para horas ou de `1.0` para números.

Se você não fornecer uma *GroupExpression*, toda a tabela será resumida em uma única linha de saída.



**Retorna**

As linhas de entrada são organizadas em grupos com os mesmos valores que as expressões `by`. Em seguida, as funções de agregação especificadas são calculadas sobre cada grupo, produzindo uma linha para cada grupo. O resultado contém as colunas `by` e pelo menos uma coluna para cada agregação calculada. (Algumas funções de agregação retornam várias colunas.)

O resultado tem a mesma quantidade de linhas que diferentes combinações de valores `by`. Se quiser resumir intervalos de valores numéricos, use `bin()` para reduzir os intervalos a valores distintos.

**Observação**

Embora você possa fornecer expressões aleatórias para as expressões de agregação e de agrupamento, é mais eficiente usar nomes de coluna simples ou aplicar `bin()` a uma coluna numérica.



### operador take

Alias de [limit](#limit-operator)


### operador top

    T | top 5 by Name desc nulls first

Retorna os primeiros *N* registros classificados pelas colunas especificadas.


**Sintaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argumentos**

* *NumberOfRows:* o número de linhas de *T* a serem retornadas.
* *Sort\_expression:* uma expressão de acordo com a qual as linhas devem ser classificadas. Normalmente é apenas um nome de coluna. Você pode especificar mais de um sort\_expression.
* `asc` ou `desc` (o padrão) pode surgir para controlar se a seleção é realmente da parte "inferior" ou "superior" do intervalo.
* Controles `nulls first` ou `nulls last` em que valores nulos são exibidos. `First` é o padrão para `asc`, `last` é o padrão para `desc`.


**Dicas**

`top 5 by name` equivale superficialmente a `sort by name | take 5`. No entanto, tem execução mais rápida e sempre retorna resultados classificados, enquanto `take` não oferece essa garantia.

### operador top-nested

    requests 
    | top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart 

Produz resultados hierárquicos, onde cada nível é uma busca detalhada do nível anterior. É útil para responder a perguntas como "quais são as cinco solicitações principais e, para cada uma delas, quais são os três principais buckets de desempenho e, para cada um deles, quais são os três países principais de onde vêm as solicitações?”

**Sintaxe**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**Argumentos**

* N:int - número de linhas a serem retornadas ou passadas para o próximo nível. Em uma consulta com três níveis onde N é 5, 3 e 3, o número total de linhas será 45.
* COLUMN - uma coluna a ser agrupada para agregação.
* AGGREGATION - uma [função de agregação](#aggregations) a ser aplicada a cada grupo de linhas. Os resultados dessas agregações determinará os maiores grupos a serem exibidos.


### operador union

     Table1 | union Table2, Table3

Usa duas ou mais tabelas e retorna as linhas de todas elas.

**Sintaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Tabela1*, *Tabela2*...
 *  O nome de uma tabela, como `requests`, ou de uma tabela definida em uma [cláusula let](#let-clause); ou
 *  Uma expressão de consulta, como `(requests | where success=="True")`
 *  Um conjunto de tabelas especificadas com um curinga. Por exemplo, `e*` formaria a união de todas as tabelas definidas nas cláusulas let anteriores cujo nome começa com “e”, juntamente com a tabela “exceções”.
* `kind`:
 * `inner` - o resultado tem o subconjunto de colunas que são comuns a todas as tabelas de entrada.
 * `outer` - o resultado tem todas as colunas que ocorrem em qualquer uma das entradas. As células que não foram definidas por uma linha de entrada são definidas como `null`.
* `withsource=`*ColumnName:* se especificado, a saída incluirá uma coluna chamada *ColumnName* cujo valor indicará qual tabela de origem contribuiu com cada linha.

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

* *T:* a entrada tabulares cujos registros devem ser filtrados.
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

> [AZURE.NOTE] Evite usar count() para localizar o número de solicitações, exceções ou outros eventos que ocorreram. Quando a [amostragem](app-insights-sampling.md) está em operação, o número de pontos de dados retidos no Application Insights é menor do que o número de eventos originais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantido.

### countif

    countif(Predicate)

Retorna uma contagem de linhas para a qual *Predicate* é avaliado como `true`.

**Dica de desempenho**: use `summarize countif(filter)` em vez de `where filter | summarize count()`

> [AZURE.NOTE] Evite usar countif() para localizar o número de solicitações, exceções ou outros eventos que ocorreram. Quando a [amostragem](app-insights-sampling.md) está em operação, o número de pontos de dados é menor que o número de eventos reais. Em vez disso, use `summarize sum(itemCount)...`. A propriedade itemCount reflete o número de eventos originais que são representados por cada ponto de dados mantido.

### dcount

    dcount( Expression [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* no grupo. (Para listar os valores distintos, use [`makeset`](#makeset)).

*Accuracy*, se for especificado, controlará o equilíbrio entre velocidade e precisão.

 * `0` = o cálculo menos preciso e mais rápido.
 * `1`, que é o padrão, equilibra a precisão e o tempo de cálculo; cerca de 0,8% de erro.
 * `2` = cálculo mais preciso e mais lento; cerca de 0,4% de erro.

**Exemplo**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* de linhas no grupo para o qual *Predicate* é verdadeiro. (Para listar os valores distintos, use [`makeset`](#makeset)).

*Accuracy*, se for especificado, controlará o equilíbrio entre velocidade e precisão.

 * `0` = o cálculo menos preciso e mais rápido.
 * `1`, que é o padrão, equilibra a precisão e o tempo de cálculo; cerca de 0,8% de erro.
 * `2` = cálculo mais preciso e mais lento; cerca de 0,4% de erro.

**Exemplo**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### makelist

    makelist(Expr [ ,  MaxListSize ] )

Retorna uma matriz `dynamic` (JSON) de todos os valores de *Expr* no grupo.

* *MaxListSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é *128*).

### makeset

    makeset(Expression [ , MaxSetSize ] )

Retorna uma matriz `dynamic` (JSON) do conjunto de valores distintos que *Expr* usa no grupo. (Dica: para contar apenas os valores distintos, use [`dcount`](#dcount).)
  
*  *MaxSetSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é *128*).

**Exemplo**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Veja também o [operador `mvexpand`](#mvexpand-operator) da função oposta.


### max, min

    max(Expr)

Calcula o número máximo de *Expr*.
    
    min(Expr)

Calcula o número mínimo de *Expr*.

**Dica**: isso fornece o mínimo ou o máximo por conta própria; por exemplo, o preço mais alto ou mais baixo. No entanto, se você quiser outras colunas na linha, por exemplo, o nome do fornecedor com o menor preço, use [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a> <a name="percentilew"></a> <a name="percentilesw"></a>
### percentile, percentiles, percentilew, percentilesw

    percentile(Expression, Percentile)

Retorna uma estimativa para a *Expression* do percentil especificado no grupo. A precisão depende da densidade da população na região do percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Como `percentile()`, mas calcula um número de valores de percentil (que é mais rápido do que calcular cada percentil individualmente).

    percentilew(Expression, WeightExpression, Percentile)

Percentil ponderado. Use isso para dados agregados previamente. `WeightExpression` é um inteiro que indica quantas linhas originais são representadas por cada linha agregada.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Como `percentilew()`, mas calcula um número de valores de percentil.

**Exemplos**


O valor de `duration`, que é maior do que 95% do conjunto de exemplo e menor do que 5% do conjunto de exemplo, calculado para cada nome de solicitação:

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

#### Percentis ponderados

Use as funções de percentil ponderada em casos em que os dados foram agregados previamente.

Por exemplo, suponha que seu aplicativo executa vários milhares de operações por segundo, e você deseja saber a sua latência. A solução mais simples seria gerar uma solicitação do Application Insights ou eventos personalizados para cada operação. Isso criaria muito tráfego, embora a amostragem adaptável poderia reduzi-lo. Mas você decide implementar uma solução ainda melhor: você vai escrever um código em seu aplicativo para agregar os dados antes de enviá-los para o Application Insights. A agregação/resumo será enviado em intervalos regulares, reduzindo a taxa de dados para talvez alguns pontos por minuto.

Seu código recebe um fluxo de medições de latência em milissegundos. Por exemplo:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Ele conta as medidas nas compartimentalizações a seguir: `{ 10, 20, 30, 40, 50, 100 }`

Periodicamente, ele faz uma série de chamadas TrackEvent, um para cada bucket, com medidas personalizadas em cada chamada:

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

No Analytics, você verá um determinado grupo de eventos como este:

`opCount` | `latency`| que significa
---|---|---
8 | 10 | = 8 operações na compartimentalização de 10 ms
6 | 20 | = 6 operações na compartimentalização de 20 ms
3 | 30 | = 3 operações na compartimentalização de 30 ms
1 | 40 | = 1 operações na compartimentalização de 40 ms

Para obter uma imagem precisa da distribuição original das latências de evento, usamos `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Os resultados são os mesmos, como se tivéssemos usado `percentiles` sem formatação no conjunto de medidas original.

> [AZURE.NOTE] Percentis ponderados não são aplicáveis a [dados de amostra](app-insights-sampling.md), em que cada linha de amostra representa uma amostra aleatória de linhas originais em vez de uma compartimentalização. As funções de percentil simples são apropriadas para dados de amostra.

#### Erro de estimativa em percentuais

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

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

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

Verifique se uma cadeia de caracteres pode ser convertida em um tipo específico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

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

Uma cadeia de caracteres que representa o tipo de armazenamento subjacente de seu argumento único. Isso é particularmente útil quando há valores da variante `dynamic`: nesse caso, `gettype()` revelará como um valor é codificado.

**Exemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

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

A função `iff()` avalia o primeiro argumento (o predicado) e retorna o valor do segundo ou do terceiro argumento, dependendo de o predicado ser `true` ou `false`. O segundo e o terceiro argumentos devem ser do mesmo tipo.

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

### toscalar

Avalia uma consulta ou uma expressão e retorna o resultado como um único valor. Essa função é útil para cálculos em etapas. Por exemplo, calcular uma contagem total de eventos e, em seguida, usá-la como uma linha de base.

**Sintaxe**

    toscalar(query)
    toscalar(scalar)

**Retorna**

O argumento avaliado. Se o argumento for uma tabela, retornará a primeira coluna da primeira linha. (É uma boa prática organizar para que o argumento tenha apenas uma coluna e uma linha).

**Exemplo**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### Literais boolianos

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Operadores boolianos

	and 
    or 

    

## Números

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

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


### abs

**Sintaxe**

	abs(x)

**Argumentos**

* x - um inteiro, um real ou um período de tempo

**Retorna**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### bin, arredmultb

Arredonda os valores até um número inteiro múltiplo de um determinado tamanho de compartimentalização. Muito usado na consulta [`summarize by`](#summarize-operator). Se você tiver um conjunto disperso de valores, eles serão agrupados em um conjunto menor de valores específicos.

Alias `floor`.

**Sintaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumentos**

* *value:* um número, uma data ou um período de tempo.
* *roundTo:* o "tamanho de compartimentalização". Um número, uma data ou um período de tempo que divide *value*.

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


`v` deve ser um número real maior do que 0. Caso contrário, nulo será retornado.

### rand

Um gerador de número aleatório.

* `rand()` - um número real entre 0,0 e 1,0
* `rand(n)` - um número inteiro entre 0 e n-1




### sqrt

A função da raiz quadrada.

**Sintaxe**

    sqrt(x)

**Argumentos**

* *x:* um número real maior ou igual a 0.

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


[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

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

Subtrai o período fornecido da hora atual UTC. Assim como `now()`, essa função pode ser usada várias vezes em uma instrução, e a hora UTC referenciada será a mesma para todas as instanciações.

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

### datepart

    datepart("Day", datetime(2015-12-14)) == 14

Extrai uma parte especificada de uma data como um número inteiro.

**Sintaxe**

    datepart(part, datetime)

**Argumentos**

* `part:String`: {"Ano", "Mês", "Dia", "Hora", "Minuto", "Segundo", "Milissegundo", "Microssegundo", "Nanossegundo"}
* `datetime`

**Retorna**

Long que representa a parte especificada.


### dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

O número ordinal do dia do mês.

**Sintaxe**

    dayofmonth(a_date)

**Argumentos**

* `a_date`: um `datetime`.


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

### dayofyear

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

O número ordinal do dia do ano.

**Sintaxe**

    dayofyear(a_date)

**Argumentos**

* `a_date`: um `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


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

* *offset:* um `timespan`, adicionado à hora UTC atual. Padrão: 0.

**Retorna**

A hora UTC atual como um `datetime`.

    now() + offset

**Exemplo**

Determina o intervalo desde o evento identificado pelo predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Verifique se uma cadeia de caracteres é uma data válida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### weekofyear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

O resultado inteiro representa o número da semana, de acordo com o padrão ISO 8601. O primeiro dia da semana é domingo e a primeira semana do ano é a que contém a primeira quinta-feira desse ano. (Os últimos dias de um ano, portanto, podem conter alguns dos dias da semana 1 do ano seguinte, ou os primeiros dias podem conter alguns dias da semana 52 ou 53 do ano anterior).


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

Literais de cadeia de caracteres ofuscados são cadeias de caracteres que serão obscurecidas pela Análise quando ela produzir a saída da cadeia de caracteres (por exemplo, durante o rastreamento). O processo de ofuscação substitui todos os caracteres ocultados por um caractere de início (`*`).

Para formar um literal de cadeia de caracteres ocultados, preceda `h` ou “H”. Por exemplo:

```
h'hello'
h@'world' 
h"hello"
```

### Comparações de cadeias de caracteres

Operador|Descrição|Diferencia maiúsculas de minúsculas|Exemplo verdadeiro
---|---|---|---
`==`|É igual a |Sim| `"aBc" == "aBc"`
`<>` `!=`|Diferente de|Sim| `"abc" <> "ABC"`
`=~`|É igual a |Não| `"abc" =~ "ABC"`
`!~`|Diferente de |Não| `"aBc" !~ "xyz"`
`has`|O lado direito (RHS) é um termo completo no lado esquerdo (LHS)|Não| `"North America" has "america"`
`!has`|RHS não é um termo completo no LHS|Não|`"North America" !has "amer"` 
`hasprefix`|RHS é um prefixo de termo no LHS|Não|`"North America" hasprefix "ame"`
`!hasprefix`|RHS não é um prefixo de termo no LHS|Não|`"North America" !hasprefix "mer"`
`contains` | RHS ocorre como uma subsequência do LHS|Não| `"FabriKam" contains "BRik"`
`!contains`| RHS não ocorre no LHS|Não| `"Fabrikam" !contains "xyz"`
`containscs` | RHS ocorre como uma subsequência do LHS|Sim| `"FabriKam" contains "Kam"`
`!containscs`| RHS não ocorre no LHS|Sim| `"Fabrikam" !contains "Kam"`
`startswith`|RHS é uma subsequência inicial do LHS.|Não|`"Fabrikam" startswith "fab"`
`!startswith`|RHS não é uma subsequência inicial do LHS.|Não|`"Fabrikam" !startswith "abr"`
`endswith`|RHS é uma subsequência terminal do LHS.|Não|`"Fabrikam" endswith "kam"`
`!endswith`|RHS não é uma subsequência terminal do LHS.|Não|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contém uma correspondência para o RHS|Sim| `"Fabrikam" matches regex "b.*k"`
`in`|Igual a qualquer um dos elementos|Sim|`"abc" in ("123", "345", "abc")`
`!in`|Diferente de qualquer um dos elementos|Sim|`"bc" !in ("123", "345", "abc")`

Use `has` ou `in` se você estiver testando a presença de um termo lexical completo, ou seja, um símbolo ou palavra alfanumérica delimitada por caracteres não alfanuméricos ou pelo início ou término do campo. `has` tem execução mais rápida do que `contains`, `startswith` ou `endswith`. A primeira dessas consultas é executada com mais rapidez:

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
* *text:* um `string` para pesquisar.
* *typeLiteral:* um literal de tipo opcional (por exemplo, `typeof(long)`). Se for fornecido, a subcadeia de caracteres extraída será convertida para esse tipo.

**Retorna**

Se *regex* encontrar uma correspondência em *text*: a subcadeia de caracteres correspondida com base no grupo de captura indicado *captureGroup*, convertida, opcionalmente, em *typeLiteral*.

Se não houver correspondência, ou se a conversão do tipo falhar: `null`.

**Exemplos**

A cadeia de caracteres de exemplo `Trace` é pesquisada em busca de uma definição para `Duration`. A correspondência é convertida em `real` e multiplicada por uma constante de tempo (`1s`) para que `Duration` seja do tipo `timespan`. Neste exemplo, é igual a 123,45 segundos:

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

True se o argumento for uma cadeia de caracteres vazia ou nula. Veja também [isnull](#isnull).


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
* *rewrite:* o regex de substituição para qualquer correspondência feita por *matchingRegex*. Use `\0` para referir-se à correspondência inteira, `\1` para o primeiro grupo de captura, `\2` e os seguintes para grupos de captura subsequentes.
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
* *requestedIndex*: um índice opcional com base em zero `int`. Se for fornecido, a matriz de cadeias de caracteres retornada conterá a subcadeia de caracteres solicitada, se ela existir.

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

* *source:* a cadeia de caracteres de origem da qual a subcadeia de caracteres será extraída.
* *startingIndex:* a posição do caractere inicial com base em zero da subcadeia de caracteres solicitada.
* *length:* um parâmetro opcional que pode ser usado para especificar o número de caracteres solicitado na subcadeia de caracteres.

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



### GUIDs

    guid(00000000-1111-2222-3333-055567f333de)


## Matrizes, objetos e dinâmico

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Este é o resultado de uma consulta em uma exceção do Application Insights. O valor em `details` é uma matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indexing:** indexar matrizes e objetos, assim como no JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Contudo, use `arraylength` e outras funções da Análise (não use ".length"!)

**Casting**: em alguns casos, é necessário converter um elemento que você extraiu de um objeto, pois seu tipo pode variar. Por exemplo, `summarize...to` precisa de um tipo específico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literals**: para criar uma matriz explícita ou um objeto de recipiente de propriedades, escreva-o como uma cadeia de caracteres JSON e o converta:

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

* `parsejson('[43, 21, 65]')` - uma matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` - um único valor de tipo dinâmico que contém um número
* `parsejson('"21"')` - um único valor de tipo dinâmico que contém uma cadeia de caracteres

Observe que, diferentemente do JavaScript, o JSON exige o uso de aspas duplas (`"`) ao redor de cadeias de caracteres. Portanto, é geralmente mais fácil citar literais de uma cadeia de caracteres codificada em JSON usando aspas simples (`'`).

Este exemplo cria um valor dinâmico e então usa seus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### Funções de objeto dinâmico

|||
|---|---|
| *value* `in` *array*| True se houver um elemento de *array* que == *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True se não houver um elemento de *array* que == *value*
|[`arraylength(`array`)`](#arraylength)| Null se não for uma matriz
|[`extractjson(`path,object`)`](#extractjson)|Usa o caminho para navegar no objeto.
|[`parsejson(`source`)`](#parsejson)| Transforma uma cadeia de caracteres JSON em um objeto dinâmico.
|[`range(`from,to,step`)`](#range)| Uma matriz de valores
|[`mvexpand` listColumn](#mvexpand-operator) | Replica uma linha para cada valor em uma lista em uma célula especificada.
|[`summarize buildschema(`column`)`](#buildschema) |Infere o esquema de tipo a partir do conteúdo da coluna
|[`summarize makelist(`column`)` ](#makelist)| Mescla os grupos de linhas e coloca os valores da coluna em uma matriz.
|[`summarize makeset(`column`)`](#makeset) | Mescla os grupos de linhas e coloca os valores da coluna em uma matriz, sem duplicação.

### Objetos dinâmicos em cláusulas let


As [cláusulas let](#let-clause) armazenam valores dinâmicos como cadeias de caracteres; portanto, essas duas cláusulas são equivalentes e ambas precisam de `parsejson` (ou `todynamic`) antes de serem usadas:

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
* Considere a possibilidade de, em vez disso, usar uma correspondência da expressão regular com [extract](#extract). Isso pode ser executado muito mais rápido, e será eficaz se JSON for produzido a partir de um modelo.
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

então, o fragmento a seguir recupera o valor do slot `duration` no objeto e, por meio disso, recupera dois slots, `duration.value` e `duration.min` (`118.0` e `110.0`, respectivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

A função `range()` (não deve ser confundida com o operador `range`) gera uma matriz dinâmica que contém uma série de valores distribuídos em espaços iguais.

**Sintaxe**

    range(start, stop, step)

**Argumentos**

* *start:* o valor do primeiro elemento na matriz resultante.
* *stop:* o valor do último elemento na matriz resultante ou o valor mínimo maior do que o último elemento na matriz resultante e dentro de um número inteiro múltiplo de *step* de *start*.
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

### Nomes

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

<!---HONumber=AcomDC_0720_2016-->