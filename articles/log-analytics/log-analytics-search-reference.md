---
title: Referência de pesquisa do Log Analytics | Microsoft Docs
description: A referência da pesquisa do Log Analytics descreve a linguagem de pesquisa e fornece as opções de sintaxe de consulta geral que você pode usar ao pesquisar expressões de dados e de filtragem para ajudar a restringir a sua pesquisa.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="log-analytics-search-reference"></a>Referência da pesquisa do Log Analytics
A seção de referência sobre a linguagem de pesquisa a seguir descreve as opções de sintaxe de consulta geral que você pode usar ao pesquisar expressões de dados e de filtragem para ajudar a restringir a sua pesquisa. Ele também descreve os comandos que você pode usar para atuar sobre os dados recuperados.

Você pode ler sobre os campos retornados nas pesquisas e as facetas que o ajudarão a analisar detalhadamente categorias de dados semelhantes na [seção Campo de pesquisa e referência de faceta](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Sintaxe de consulta geral
Sintaxe:

```
filterExpression | command1 | command2 …
```

A expressão de filtro (`filterExpression`) define a condição "where" para a consulta. Os comandos se aplicam aos resultados retornados pela consulta. Vários comandos devem ser separados pelo caractere de barra vertical (|).

### <a name="general-syntax-examples"></a>Exemplos de sintaxe geral
Exemplos:

```
system
```

Essa consulta retorna resultados que contêm a palavra "sistema" em qualquer campo que tenha sido indexado por pesquisa de texto completo ou de termos.

> [!NOTE]
> Nem todos os campos são indexados dessa maneira, mas os campos de texto mais comuns (como nomes e descrições) normalmente são.
> 
> 

```
system error
```

Essa consulta retorna resultados que contêm as palavras *sistema* e *erro*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Essa consulta retorna resultados que contêm as palavras *sistema* e *erro*. Em seguida, ela classifica os resultados pelo campo *ManagementGroupName* (en ordem crescente) e pelo campo *TimeGenerated* (em ordem decrescente). Ela usa apenas os 10 primeiros resultados.

> [!IMPORTANT]
> Todos os nomes de campo e os valores para os campos de cadeia de caracteres e de texto diferenciam maiúsculas de minúsculas.
> 
> 

## <a name="filter-expression"></a>Expressão do filtro...
As subseções a seguir explicam as expressões de filtro.

### <a name="string-literals"></a>Literais de cadeia de caracteres
Um literal de cadeia de caracteres é qualquer cadeia de caracteres que não seja reconhecida pelo analisador como uma palavra-chave ou um tipo de dados predefinido (por exemplo, um número ou data).

Exemplos:

```
These all are string literals
```

Essa consulta pesquisa resultados que contenham ocorrências de todas as cinco palavras. Para executar uma pesquisa de cadeia de caracteres complexa, coloque o literal da cadeia de caracteres entre aspas, por exemplo:

```
" Windows Server"
```

Isso retorna somente os resultados com correspondências exatas para "Windows Server".

### <a name="numbers"></a>Números
O analisador dá suporte a inteiros decimais e à sintaxe de número de ponto flutuante para campos numéricos.

Exemplos:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="date/time"></a>Data/hora
Cada parte dos dados no sistema tem uma propriedade *TimeGenerated* que representa a data e hora original do registro. Alguns tipos de dados podem ter mais campos de data/hora (por exemplo, *LastModified*).

O seletor de Gráfico/Tempo da linha do tempo no Log Analytics mostra uma distribuição de resultados ao longo do tempo (de acordo com a consulta atual que está sendo executada) com base no campo *TimeGenerated* . Os campos de data/hora têm um formato de cadeia de caracteres específico que pode ser usado em consultas para restringi-la a um determinado período. Você também pode usar a sintaxe para se referir a intervalos de tempo relativo (por exemplo, "entre 3 dias atrás e 2 horas atrás").

Sintaxe:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Exemplo:

```
TimeGenerated:2013-10-01T12:20
```

O comando anterior retorna somente os registros com um valor *TimeGenerated* de exatamente 12:20 em 1º de outubro de 2013. É improvável que ele venha a fornecer qualquer resultado, mas você pegou a ideia.

O analisador também dá suporte ao valor de data/hora mnemônico AGORA.

Novamente, é improvável que isso gere um resultado porque os dados não passam pelo sistema tão rápido.

Esses exemplos são blocos de construção a serem usados para datas relativas e absolutas. Nas próximas três subseções, explicaremos como usá-los em filtros mais avançados, com exemplos que usam intervalos de datas relativas.

### <a name="date/time-math"></a>Cálculos de data/hora
Use os operadores de cálculo de data/hora para deslocar ou arredondar o valor de data/hora usando cálculos simples de data/hora.

Sintaxe:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| Operador | Descrição |
| --- | --- |
| / |Arredonda data/hora para a unidade especificada. Exemplo: AGORA/DIA arredonda a data/hora atual para a meia-noite do dia atual. |
| + ou - |Desloca Data/Hora no número especificado de unidades. Exemplos: NOW + 1HOUR desloca a data/hora atual uma hora à frente. 2013-10-01t12: 00-10DAYS desloca o valor de data em 10 dias. |

Você pode encadear os operadores de cálculo de data/hora, por exemplo:

```
NOW+1HOUR-10MONTHS/MINUTE
```

A tabela a seguir lista as unidades com suporte de data/hora.

| Unidade de data/hora | Descrição |
| --- | --- |
| YEAR, YEARS |Arredonda para o ano atual ou desloca pelo número especificado de anos. |
| MONTH, MONTHS |Arredonda para o mês atual ou desloca pelo número especificado de meses. |
| DAY, DAYS, DATE |Arredonda para o dia do mês atual ou desloca pelo número especificado de dias. |
| HOUR, HOURS |Arredonda para a hora atual ou desloca pelo número especificado de horas. |
| MINUTE, MINUTES |Arredonda para o minuto atual ou desloca pelo número especificado de minutos. |
| SECOND, SECONDS |Arredonda para o segundo atual ou desloca pelo número especificado de segundos. |
| MILLISECOND, MILLISECONDS, MILLI, MILLIS |Arredonda para o milissegundo atual ou desloca pelo número especificado de milissegundos. |

### <a name="field-facets"></a>Facetas de campo
Usando as facetas de campo, você pode especificar o critério de pesquisa para campos específicos e seus valores exatos, em vez de escrever consultas com "texto livre" para vários termos em todo o índice. Já usamos essa sintaxe em vários exemplos nos parágrafos anteriores. Aqui, fornecemos exemplos mais complexos.

**Sintaxe**

```
field:value
```

```
field=value
```

**Descrição**

Pesquisa o valor específico no campo. O valor pode ser um literal de cadeia de caracteres, número ou data/hora.

Exemplo:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Sintaxe**

*campo>valor*

*campo<valor*

*campo>=valor*

*campo<=valor*

*campo!=valor*

**Descrição**

Fornece comparações.

Exemplo:

```
TimeGenerated>NOW+2HOURS
```

**Sintaxe**

```
field:[from..to]
```

**Descrição**

Fornece facetamento de intervalo.

Exemplo:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="logical-operators"></a>Operadores lógicos
As linguagens de consulta dão suporte aos operadores lógicos (*AND*, *OR* e *NOT*) e seus alias de operadores em C (*&&*, *||* e *!*), respectivamente. Você pode usar parênteses para agrupar esses operadores.

Exemplos:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Você pode omitir o operador lógico para os argumentos de filtro de nível superior. Nesse caso, o operador AND será assumido.

| Expressão do filtro... | Equivalente a |
| --- | --- |
| erro do sistema |erro do sistema AND |
| sistema "Windows Server" OR Gravidade: 1 |sistema AND (“Windows Server” OR Gravidade: 1) |

### <a name="wildcarding"></a>Recurso de curinga
O idioma de consulta dá suporte usando o caractere (*\*) para representar um ou mais caracteres para um valor em uma consulta.

Exemplos:

 Localize todos os computadores com "SQL" no nome, como "Redmond-SQL".

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> Os caracteres curinga não podem ser usados entre aspas atualmente. Mensagem=`"*This text*"` considerará o (\*) usado como um caractere literal (\*).
> 
> ## <a name="commands"></a>Comandos
> 

Os comandos se aplicam aos resultados retornados pela consulta. Use o caractere de barra vertical (|) para aplicar um comando aos resultados obtidos. Vários comandos devem ser separados pelo caractere de barra vertical.

> [!NOTE]
> Os nomes de comando podem ser escritos em letras maiúsculas ou minúsculas, ao contrário de nomes de campos e dados.
> 
> 

### <a name="sort"></a>Classificar
Sintaxe:

    sort field1 asc|desc, field2 asc|desc, …

Classifica os resultados por campos específicos. O prefixo crescente/decrescente é opcional. Se eles forem omitidos, a ordem de classificação *crescente* será presumida. Se uma consulta não usar o comando *Sort* explicitamente, Sort **TimeGenerated** desc será o comportamento padrão e sempre retornará os resultados mais recentes primeiro.

### <a name="top/limit"></a>Superior/limite
Sintaxe:

    top number


    limit number
Limita a resposta aos N principais resultados.

Exemplo:

    Type:Alert errors detected | top 10

Retorna os 10 primeiros resultados de correspondência.

### <a name="skip"></a>Skip
Sintaxe:

    skip number

Ignora o número de resultados listados.

Exemplo:

    Type:Alert errors detected | top 10 | skip 200

Retorna os 10 primeiros resultados de correspondência, começando no resultado 200.

### <a name="select"></a>Selecionar
Sintaxe:

    select field1, field2, ...

Limita os resultados aos campos escolhidos.

Exemplo:

    Type:Alert errors detected | select Name, Severity

Limita os campos de resultados retornados para *Name* e *Severity*.

### <a name="measure"></a>Medida
O comando *measure* é usado para aplicar funções estatísticas aos resultados brutos da pesquisa. Isso é muito útil para obter as exibições *agrupar por* dos dados. Quando você usa o comando *measure* , o Log Analytics exibe uma tabela com resultados agregados.

Sintaxe:

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Agrega os resultados por *groupField* e calcula os valores de medida agregados usando *aggregatedField*.

| Função estatística Medida | Descrição |
| --- | --- |
| *aggregateFunction* |O nome da função aggregate (não diferencia maiúsculas de minúsculas). Há suporte para as seguintes funções agregadas: COUNT MAX MIN SUM AVG STDDEV COUNTDISTINCT PERCENTILE## ou PCT## (## é um número entre 1 e 99) |
| *aggregatedField* |O campo que está sendo agregado. Esse campo é opcional para a função de agregação COUNT, mas deve ser um campo numérico existente para SUM, MAX, MIN, AVG STDDEV ou PERCENTILE## ou pct## (## é um número entre 1 e 99). |
| *fieldAlias* |O alias (opcional) para o valor agregado calculado. Se não for especificado, o nome do campo será AggregatedValue. |
| *groupField* |O nome do campo pelo qual o conjunto de resultados é agrupado. |
| *Intervalo* |O intervalo de tempo no formato:**nnnNAME** , em que nnn é um número inteiro positivo. **NaME** é o nome do intervalo. Os nomes de intervalo com suporte incluem (com distinção entre maiúsculas e minúsculas): MILLISECOND[S] SECOND[S] MINUTE[S] HOUR[S] DAY[S] MONTH[S] YEAR[S] |

A opção de intervalo só pode ser usada em campos de grupo de data/hora (como *TimeGenerated* e *TimeCreated*). Atualmente, isso não é imposto pelo serviço, mas um campo sem data/hora que é passado para o back-end causará um erro de tempo de execução. Quando a validação do esquema é implementada, a API do serviço rejeita as consultas que usam campos sem data/hora para agregação de intervalo. A implementação atual de *Measure* dá suporte ao agrupamento de intervalo de qualquer função de agregação.

Se a cláusula BY for omitida, mas um intervalo for especificado (como uma segunda sintaxe), o campo *TimeGenerated* será considerado por padrão.

Exemplos:

**Exemplo 1**

    Type:Alert | measure count() as Count by ObjectId

*Explicação*

Agrupa os alertas por *ObjectID* e calcula o número de alertas para cada grupo. O valor agregado é retornado como o campo *Count* (alias).

**Exemplo 2**

    Type:Alert | measure count() interval 1HOUR

*Explicação*

Agrupa os alertas por intervalos de uma hora usando o campo *TimeGenerated* e retorna o número de alertas em cada intervalo.

**Exemplo 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explicação*

Igual ao exemplo anterior, mas com um alias de campo agregado (*AlertsPerHour*).

**Exemplo 4**

    * | measure count() by TimeCreated interval 5DAYS

*Explicação*

Agrupa os resultados por intervalos de cinco dias usando o campo *TimeCreated* e retorna o número de resultados em cada intervalo.

**Exemplo 5**

    Type:Alert | measure max(Severity) by WorkflowName

*Explicação*

Agrupa os alertas por cargas de trabalho e retorna o valor máximo de severidade de alerta para cada fluxo de trabalho.

**Exemplo 6**

    Type:Alert | measure min(Severity) by WorkflowName

*Explicação*

Igual ao exemplo anterior, mas com a função de agregação *Min* .

**Exemplo 7**

    Type:Perf | measure avg(CounterValue) by Computer

*Explicação*

Agrupa o desempenho por Computador e calcula a média (avg).

**Exemplo 8**

    Type:Perf | measure sum(CounterValue) by Computer

*Explicação*

Igual ao exemplo anterior, mas usa *Sum*.

**Exemplo 9**

    Type:Perf | measure stddev(CounterValue) by Computer

*Explicação*

Igual ao exemplo anterior, mas usa *STDDEV*.

**Exemplo 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

*Explicação*

Igual ao exemplo anterior, mas usa *PERCENTILE70*.

**Exemplo 11**

    Type:Perf | measure pct70(CounterValue) by Computer

*Explicação*

Igual ao exemplo anterior, mas usa *PCT70*. Observe que *PCT##* é apenas um alias para a função *PERCENTILE##*.

**Exemplo 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

*Explicação*

Agrupa o desempenho primeiro por Computador e segundo por CounterName, além de calcular a média (avg).

**Exemplo 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explicação*

Obtém os cinco principais fluxos de trabalho com o número máximo de alertas.

**Exemplo 14**

    * | measure countdistinct(Computer) by Type

*Explicação*

Conta o número de computadores exclusivos fazendo relatório para cada Type.

**Exemplo 15**

    * | measure countdistinct(Computer) Interval 1HOUR

*Explicação*

Conta o número de computadores exclusivos fazendo relatório a cada hora.

**Exemplo 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*Explicação*

Agrupa % de Tempo do Processador por Computador e retorna a média para cada período de uma hora.

**Exemplo 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*Explicação*

Agrupa W3CIISLog por método e retorna o valor máximo para cada período de cinco minutos.

**Exemplo 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*Explicação*

Agrupa % de Tempo do Processador por Computador e retorna o mínimo, a média, percentil 75 e máximo para cada período de uma hora.

**Exemplo 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

*Explicação*

Agrupa % de Tempo do Processador primeiro por computador e segundo por Nome da instância, além de retornar o mínimo, a média, percentil 75 e máximo para cada período de uma hora

### <a name="where"></a>Where
Sintaxe:

```
**where** AggregatedValue>20
```

Pode ser usado somente depois de um comando *Measure* para filtrar ainda mais os resultados agregados que a função de agregação *Measure* produziu.

Exemplos:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### <a name="in"></a>IN
Sintaxe:

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Descrição: essa sintaxe permite que você crie uma agregação e alimente a lista de valores com essa agregação em outra pesquisa externa (primária) que procurará eventos com esses valores. Você pode fazer isso colocando a pesquisa interna entre colchetes e repassando os resultados como valores possíveis para um campo na pesquisa externa usando o operador IN.

Exemplo de consulta interna: *computadores atualmente em falta com atualizações de segurança* com a seguinte consulta de agregação:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

A consulta final que localiza *todos os eventos do Windows para computadores que atualmente estão em falta com atualizações de segurança* seria semelhante a:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="dedup"></a>Dedup
**Sintaxe**

    Dedup FieldName

**Descrição** Retorna o primeiro documento encontrado para cada valor exclusivo de determinado campo.

**Exemplo**

    Type=Event | sort TimeGenerated DESC | Dedup EventID

O exemplo acima retorna um evento (a versão mais recente porque usamos DESC em TimeGenerated) por EventID

### <a name="extend"></a>Extend
**Descrição** Permite criar campos de tempo de execução em consultas

**Exemplo 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Mostrar pontuação de recomendação ponderada para as recomendações da Avaliação do SQL

**Exemplo 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Mostrar o valor do contador em KBs em vez de Bytes

**Exemplo 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Dimensionar o valor de WireData TotalBytes de modo que todos os resultados fiquem entre 0 e 100.

**Exemplo 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
Tag Perf Counter Values less than 50% las LOW and others as HIGH
```

**Funções com Suporte**

| Função | Descrição | Exemplos de Sintaxe |
| --- | --- | --- |
| abs |Retorna o valor absoluto do valor ou da função especificada. |`abs(x)` <br> `abs(-5)` |
| acos |Retorna o arco cosseno de um valor ou uma função |`acos(x)` |
| e |Retornará um valor true se e somente se todos os seus operandos forem avaliados como true. |`and(not(exists(popularity)),exists(price))` |
| asin |Retorna o arco seno de um valor ou uma função |`asin(x)` |
| atan |Retorna o arco tangente de um valor ou uma função |`atan(x)` |
| atan2 |Retorna o ângulo resultante da conversão das coordenadas retangulares x, y em coordenadas polares |`atan2(x,y)` |
| cbrt |Raiz cúbica |`cbrt(x)` |
| ceil |Arredonda para cima para um número inteiro |`ceil(x)`  <br> `ceil(5.6)` – Retorna 6 |
| cos |Retorna o cosseno de um ângulo |`cos(x)` |
| cosh |Retorna o cosseno hiperbólico de um ângulo |`cosh(x)` |
| def |def é uma abreviatura para padrão. Retornará o valor do campo "campo" ou, se o campo não existir, retornará o valor padrão especificado e gerará o primeiro valor, em que: `exists()==true`. |`def(rating,5)` – essa função def() retornará a classificação ou, se não houver nenhuma classificação especificada no documento, retornará 5 <br> `def(myfield, 1.0)` – equivalente a `if(exists(myfield),myfield,1.0)` |
| deg |Converter radianos em graus |`deg(x)` |
| div |`div(x,y)` divide x por y. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |Retorna a distância entre dois vetores (pontos) em um espaço n-dimensional. Usa a potência, além de duas ou mais instâncias de ValueSource, e calcula as distâncias entre dois vetores. Cada ValueSource deve ser um número. Um número par de instâncias ValueSource deve ser passado e o método pressupõe que a primeira metade representa o primeiro vetor e a segunda metade representa o segundo vetor. |`dist(2, x, y, 0, 0)` – calcula a distância Euclidiana entre (0,0) e (x, y) para cada documento. <br> `dist(1, x, y, 0, 0)` – calcula a distância Manhattan (taxi) entre (0,0) e (x, y) para cada documento. <br> `dist(2,,x,y,z,0,0,0)` – a distância Euclidiana entre (0,0,0) e (x,y,z) para cada documento.<br>`dist(1,x,y,z,e,f,g)` – distância Manhattan entre (x,y,z) e (e,f,g), em que cada letra é um nome de campo. |
| exists |Retorna TRUE se existir algum membro do campo. |`exists(author)` – retorna TRUE para qualquer documento que tenha um valor no campo "author".<br>`exists(query(price:5.00))` – retornará TRUE se "price" corresponder a "5.00". |
| exp |Retorna o número de Euler elevado à potência x |`exp(x)` |
| floor |Arredonda para baixo para um número inteiro |`floor(x)`  <br> `floor(5.6)` – Retorna 5 |
| hypo |Retorna sqrt(sum(pow(x,2),pow(y,2))) sem estouro intermediário ou estouro negativo |`hypo(x,y)`  <br> ` |
| if |Permite consultas de função condicional. Em `if(test,value1,value2)` – teste é ou se refere a um valor lógico ou a uma expressão que retorna um valor lógico (TRUE ou FALSE).  `value1` é o valor retornado pela função se o teste retorna TRUE. `value2` é o valor retornado pela função se o teste retorna FALSE. Uma expressão pode ser qualquer função que gera valores boolianos ou até mesmo funções que retornam valores numéricos. Em todo caso, o valor 0 será interpretado como false, ou cadeias de caracteres, caso em que a cadeia de caracteres vazia é interpretada como false. |`if(termfreq(cat,'electronics'),popularity,42)` – essa função verifica cada documento para ver se ele contém o termo "electronics" no campo cat. Se isso acontecer, o valor do campo popularidade será retornado. Do contrário, o valor retornado será 42. |
| linear |Implementa `m*x+c` , em que m e c são constantes e x é uma função arbitrária. Isso é equivalente a `sum(product(m,x),c)`, mas um pouco mais eficiente, pois é implementado como uma única função. |`linear(x,m,c) linear(x,2,4)` retorna `2*x+4` |
| ln |Retorna o log natural da função especificada |`ln(x)` |
| log |Retorna o log de base 10 da função especificada. |`log(x)   log(sum(x,100))` |
| map |Mapeia todos os valores de uma função de entrada x que se enquadram entre min e max, inclusive no destino especificado. Os argumentos min e max devem ser constantes. Os argumentos padrão e destino podem ser constantes ou funções. Se o valor de x estiver entre min e max, o valor de x será retornado ou um valor padrão será retornado se especificado como um quinto argumento. |`map(x,min,max,target) map(x,0,0,1)` : altera todos os valores de 0 a 1. Isso pode ser útil para lidar com valores padrão 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)` – altera os valores entre 0 e 100 para 1 e todos os outros valores para -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))` : altera valores entre 0 e 100 para x + 599 e todos os outros valores para a frequência do termo “solr” no texto do campo. |
| max |Retorna o valor numérico máximo de várias funções ou constantes aninhadas, que são especificadas como argumentos: `max(x,y,...)`. A função max também pode ser útil para empurrar outra função ou campo para trás em alguma constante especificada.  Use a sintaxe `field(myfield,max)` para escolher o valor máximo de um único campo de múltiplos valores. |`max(myfield,myotherfield,0)` |
| Min |Retorna o valor numérico mínimo de várias funções aninhadas de constantes, que são especificadas como argumentos: `min(x,y,...)`. A função min também pode ser útil para fornecer um "limite superior" em uma função que usa uma constante. Use a sintaxe `field(myfield,min)` para escolher o valor mínimo de um único campo de múltiplos valores. |`min(myfield,myotherfield,0)` |
| mod |Computa o módulo da função x pela função y. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |Retorna os milissegundos de diferença entre seus argumentos. As datas são relativas ao momento POSIX ou Unix, meia-noite de 1º de janeiro de 1970 UTC. Os argumentos podem ser o nome de um TrieDateField indexado ou matemática de data baseada em uma data constante ou NOW. `ms()` é equivalente a `ms(NOW)`, o número de milissegundos desde a época. `ms(a)` : retorna o número de milissegundos desde o momento que o argumento representa. `ms(a,b)` retorna o número de milissegundos entre a ocorrência de b e a ocorrência posterior de a, que é `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| não |O valor negado logicamente da função encapsulada. |`not(exists(author))` – TRUE somente quando `exists(author)` é false. |
| ou o |Uma disjunção lógica. |`or(value1,value2)` : VERDADEIRO se value1 ou value2 for verdadeiro. |
| pow |Gera a base especificada elevada à potência especificada. `pow(x,y)` eleva x à y potência. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)` : o mesmo que sqrt. |
| product |Retorna o produto de vários valores ou funções, que são especificados em uma lista separada por vírgulas. `mul(...)` também pode ser usado como um alias para essa função. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Executa uma função recíproca com `recip(x,m,a,b)` implementando `a/(m*x+b)`, em que m, a e b são constantes e x é uma função arbitrariamente complexa. Quando a e b são iguais e x>=0, essa função tem um valor máximo de 1 que cai conforme x aumenta. O aumento do valor de a e b juntos resulta na movimentação da função inteira para uma parte menos arredondada da curva. Essas propriedades podem tornar isso uma função ideal para impulsionar documentos mais recentes quando x for `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |Converter graus em radianos |`rad(x)` |
| rint |Arredonda para o inteiro mais próximo |`rint(x)`  <br> `rint(5.6)` – Retorna 6 |
| sin |Retorna o seno de um ângulo |`sin(x)` |
| sinh |Retorna o seno hiperbólico de um ângulo |`sinh(x)` |
| scale |Dimensiona valores da função x de modo que eles se encaixem entre o minTarget especificado e maxTarget, inclusive. A implantação atual percorre todos os valores de função para obter min e max para que ela possa escolher a escala correta. A implantação atual não consegue perceber quando documentos foram excluídos ou documentos que não têm valor. Ela usa valores 0,0 para esses casos. Isso significa que se os valores são normalmente todos maiores que 0,0, um ainda pode acabar com 0,0 como o valor mínimo para mapear. Nesses casos, uma função `map()` apropriada pode ser usada como solução alternativa a fim de alterar 0,0 para um valor no intervalo real, como mostrado aqui: `scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)` : dimensiona os valores de x, de modo que todos os valores fiquem entre 1 e 2, inclusive. |
| sqrt |Retorna a raiz quadrada do valor ou da função especificada. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Calcula a distância entre duas cadeias de caracteres. Usa a interface StringDistance do verificador ortográfico do Lucene e dá suporte a todas as implantações disponíveis no pacote, além de permitir que aplicativos conectem suas próprias interfaces por meio do recurso de carregamento do Solr. strdist usa `(string1, string2, distance measure)`. Os valores possíveis para a medida de distância são: <br>jw: Jaro-Winkler<br>edit: distância Levenstein ou de Edição<br>ngram: o NGramDistance, se especificado, poderá opcionalmente passar no tamanho ngram também. O padrão é 2.<br>FQN: nome de classe totalmente qualificado para uma implementação da interface StringDistance. Deve ter um construtor não arg. |`strdist("SOLR",id,edit)` |
| sub |Retorna x-y de `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |Retorna a soma de vários valores ou funções, que são especificados em uma lista separada por vírgulas. `add(...)` pode ser usado como um alias para esta função. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |Retorna o número de vezes que o termo aparece no campo para esse documento. |termfreq(text,'memory') |
| tan |Retorna a tangente de um ângulo |`tan(x)` |
| tanh |Retorna a tangente hiperbólica de um ângulo |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Campo de pesquisa e referência da faceta
Quando você usar a Pesquisa de Log para localizar dados, os resultados exibirão vários campos e facetas. No entanto, algumas informações que você vê talvez não pareçam muito descritivas. Você pode usar as informações a seguir para ajudá-lo a entender os resultados.

| Campo | Tipo de pesquisa | Descrição |
| --- | --- | --- |
| TenantId |Todos |Usado para particionar dados |
| TimeGenerated |Todos |Usado para orientar a linha do tempo, timeselectors (na pesquisa e em outras telas). Representa quando a porção de dados foi gerada (normalmente no agente). O tempo é expresso no formato ISO e é sempre UTC. No caso de ‘tipos’ baseados em instrumentação existente (ou seja, eventos em um log), isso é normalmente o tempo real em que a entrada de log/linha/registro foi registrada em log; para alguns dos outros tipos que são produzidos através de pacotes de gerenciamento ou na nuvem, ou seja, recomendações/alertas/updateagent/etc., isso é o tempo em que essa nova porção dos dados com um instantâneo de alguma configuração foi coletada ou uma recomendação/alerta foi gerada com base nela. |
| EventID |Evento |EventID no log de eventos do Windows |
| EventLog |Evento |Log de eventos onde o evento foi registrado pelo Windows |
| EventLevelName |Evento |Crítico/aviso/informação/êxito |
| EventLevel |Evento |Valor numérico de crítico/aviso/informação/êxito (use EventLevelName em vez disso para consultas mais legíveis/mais fáceis) |
| SourceSystem |Todos |De onde os dados vêm (no sentido de modo 'anexar' para o serviço, ou seja, o Operations Manager, o OMS (= os dados são gerados na nuvem), o Armazenamento do Azure (dados provenientes do WAD) e assim por diante |
| ObjectName |PerfHourly |Nome de objeto de desempenho do Windows |
| InstanceName |PerfHourly |Nomes da instância do contador de desempenho do Windows |
| CounteName |PerfHourly |Nome do contador de desempenho do Windows |
| ObjectDisplayName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nome de exibição do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto pelo Insights Operacionais, ou pela qual o alerta foi gerado |
| RootObjectName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nome de exibição do pai do pai (em uma relação de hospedagem dupla: por exemplo, SqlDatabase hospedado pelo SqlInstance hospedado pelo computador do Windows) do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto pelo Insights Operacionais, ou pela qual o alerta foi gerado |
| Computador |Maioria dos tipos |Nome do computador a que pertencem os dados |
| DeviceName |ProtectionStatus |O nome do computador a que os dados pertencem (mesmo que 'Computador') |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |A classificação de status de ameaça é uma representação numérica do status de ameaça e, de forma semelhante a códigos de resposta HTTP, deixamos lacunas entre os números (motivo pelo qual nenhuma ameaça é 150, e não 100 ou 0) para que tenhamos um espaço para adicionar novos estados. Quando fazemos um acúmulo de estados de ameaça e status de proteção, queremos mostrar o pior estado em que o computador esteve durante o período de tempo selecionado. Usamos os números para classificar os diferentes estados e nos permitir procurar o registro com o número mais alto. |
| ThreatStatus |ProtectionStatus |Descrição do ThreatStatus, mapeia 1:1 com ThreatStatusRank |
| TypeofProtection |ProtectionStatus |Produto antimalware detectado no computador: nenhum, ferramenta de remoção de Malware da Microsoft, Forefront, etc. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus, RequiredUpdate |ID de serviço de integridade para o agente desse computador |
| HealthServiceId |Maioria dos tipos |ID de serviço de integridade para o agente desse computador |
| ManagementGroupName |Maioria dos tipos |Nome do grupo de gerenciamento para agentes anexados ao Operations Manager; caso contrário, será nulo/espaço em branco |
| ObjectType |ConfigurationObject |Type (como em 'type'/class do pacote de gerenciamento do Operations Manager) para esse objeto descoberto pela avaliação de configuração do Log Analytics |
| UpdateTitle |RequiredUpdate |Nome da atualização que foi encontrada não instalada |
| PublishDate |RequiredUpdate |Quando a atualização foi publicada no Microsoft Update? |
| Servidor |RequiredUpdate |O nome do computador a que os dados pertencem (mesmo que 'Computador') |
| product |RequiredUpdate |Produto ao qual a atualização se aplica |
| UpdateClassification |RequiredUpdate |Tipo de atualização (pacote cumulativo de atualizações, service pack, etc.) |
| KBID |RequiredUpdate |ID do artigo KB que melhor descreve essa prática recomendada ou atualização |
| WorkflowName |ConfigurationAlert |Nome da regra ou monitor que produziu o alerta |
| Severity |ConfigurationAlert |Severidade do alerta |
| Prioridade |ConfigurationAlert |Prioridade do alerta |
| IsMonitorAlert |ConfigurationAlert |Esse alerta foi gerado por um monitor (true) ou por uma regra (false)? |
| AlertParameters |ConfigurationAlert |XML com os parâmetros do alerta do Log Analytics |
| Contexto |ConfigurationAlert |XML com o “contexto” do alerta do Log Analytics |
| Carga de trabalho |ConfigurationAlert |Tecnologia ou 'carga de trabalho' a que o alerta se refere |
| AdvisorWorkload |Recomendações |Tecnologia ou 'carga de trabalho' a que se refere a recomendação |
| Descrição |ConfigurationAlert |Descrição do alerta (curta) |
| DaysSinceLastUpdate |UpdateAgent |Quantos dias atrás (relativo ao 'TimeGenerated' desse registro) esse agente instalou qualquer atualização do WSUS/Microsoft Update? |
| DaysSinceLastUpdateBucket |UpdateAgent |Com base em DaysSinceLastUpdate, uma categorização em 'buckets de tempo' de quanto tempo atrás um computador teve instalada pela última vez qualquer atualização do WSUS/Microsoft Update |
| AutomaticUpdateEnabled |UpdateAgent |A verificação de atualização automática está habilitada ou desabilitada nesse agente? |
| AutomaticUpdateValue |UpdateAgent |A verificação de atualização automática está definida para baixar automaticamente e instalar, somente baixar ou somente instalar? |
| WindowsUpdateAgentVersion |UpdateAgent |Número da versão do agente do Microsoft Update |
| WSUSServer |UpdateAgent |A qual servidor WSUS se destina esse agente de atualização? |
| OSVersion |UpdateAgent |Versão do sistema operacional sendo executada nesse agente de atualização |
| NaME |Recomendação, ConfigurationObjectProperty |Nome/título da recomendação ou nome da propriedade da avaliação de configuração do Log Analytics |
| Valor |ConfigurationObjectProperty |Valor de uma propriedade da Avaliação de Configuração do Log Analytics |
| KBLink |Recomendações |URL para o artigo KB que melhor descreve essa prática recomendada ou atualização |
| RecommendationStatus |Recomendações |As recomendações estão entre os poucos tipos cujos registros são 'atualizados', não apenas adicionados ao índice de pesquisa. Esse status se altera quando a recomendação está ativa/aberta ou quando o Log Analytics detecta que foi resolvido. |
| RenderedDescription |Evento |Descrição renderizada (texto reutilizado com parâmetros populados) de um evento do Windows |
| ParameterXml |Evento |XML com os parâmetros na seção 'dados' de um evento do Windows (como visto no visualizador de eventos) |
| EventData |Evento |XML com a seção inteira de 'dados' de um evento do Windows (como visto no visualizador de eventos) |
| Fonte |Evento |Origem do log de eventos que gerou o evento |
| EventCategory |Evento |Categoria do evento, diretamente do log de eventos do Windows |
| UserName |Evento |Nome de usuário de evento do Windows (normalmente, NT AUTHORITY\LOCALSYSTEM) |
| SampleValue |PerfHourly |Valor médio para a agregação por hora de um contador de desempenho |
| Min |PerfHourly |Valor mínimo no intervalo por hora de uma agregação por hora do contador de desempenho |
| max |PerfHourly |Valor máximo no intervalo por hora de uma agregação por hora do contador de desempenho |
| Percentile95 |PerfHourly |O 95º valor de percentil para o intervalo de hora de uma agregação por hora do contador de desempenho |
| SampleCount |PerfHourly |Quantas amostras de contador de desempenho 'brutas' foram usadas para produzir esse registro de agregação por hora |
| Ameaça |ProtectionStatus |Nome de malware encontrado |
| StorageAccount |W3CIISLog |A conta de armazenamento do Azure de onde o log foi lido |
| AzureDeploymentID |W3CIISLog |ID de implantação do Azure do serviço de nuvem a que o log pertence |
| Função |W3CIISLog |A função do serviço de nuvem do Azure a que o log pertence |
| RoleInstance |W3CIISLog |RoleInstance da função do Azure a que pertence o log |
| sSiteName |W3CIISLog |Site do IIS a que pertence o log (notação de metabase); o campo s-sitename no log original |
| sComputerName |W3CIISLog |O campo s-computername no log original |
| sIP |W3CIISLog |Endereço IP do servidor ao qual a solicitação HTTP foi endereçada. O campo s-ip no log original |
| csMethod |W3CIISLog |Método HTTP (GET/POST/etc.) usado pelo cliente na solicitação HTTP. O cs-method no log original |
| cIP |W3CIISLog |Endereço IP do cliente de onde veio a solicitação HTTP. O campo c-ip no log original |
| csUserAgent |W3CIISLog |Agente-usuário HTTP declarado pelo cliente (navegador ou outros). O cs-user-agent no log original |
| scStatus |W3CIISLog |Código de status HTTP (200/403/500/etc.) retornado pelo servidor para o cliente. O cs-status no log original |
| TimeTaken |W3CIISLog |Quanto tempo (em milissegundos) a solicitação levou para ser concluída. O campo timetaken no log original |
| csUriStem |W3CIISLog |O Uri relativo (sem hospedar o endereço, ou seja, '/search ') que foi solicitado. O campo cs-uristem no log original |
| csUriQuery |W3CIISLog |Consulta URI. As consultas URI somente são necessárias para páginas dinâmicas, como as páginas ASP; portanto, esse campo geralmente contém um hífen para páginas estáticas. |
| sPort |W3CIISLog |Porta do servidor que recebeu a solicitação HTTP (e é ouvida pelo IIS, já que ele a percebeu) |
| csUserName |W3CIISLog |Nome de usuário autenticado, se a solicitação for autenticada e não anônima |
| csVersion |W3CIISLog |Versão do protocolo HTTP usada na solicitação (ou seja, HTTP/1.1 OK |
| csCookie |W3CIISLog |Informações de cookie |
| csReferer |W3CIISLog |Site que o usuário visitou por último. Esse site fornece um link para o site atual. |
| csHost |W3CIISLog |Cabeçalho de host (por exemplo, 'www.mysite.com') que foi solicitado |
| scSubStatus |W3CIISLog |Código de erro de substatus |
| scWin32Status |W3CIISLog |Código de Status do Windows |
| csBytes |W3CIISLog |Bytes enviados na solicitação do cliente para o servidor |
| scBytes |W3CIISLog |Bytes retornados em resposta do servidor para o cliente |
| ConfigChangeType |ConfigurationChange |Tipo de alteração (WindowsServices/Software /etc.) |
| ChangeCategory |ConfigurationChange |Categoria da alteração (modificado/adicionado/removido) |
| SoftwareType |ConfigurationChange |Tipo de software (atualização/aplicativo) |
| SoftwareName |ConfigurationChange |Nome do software (aplicável somente às alterações de software) |
| Editor |ConfigurationChange |Fornecedor que publica o software (aplicável somente às alterações de software) |
| SvcChangeType |ConfigurationChange |Tipo de alteração que foi aplicada a um serviço do Windows (State / StartupType / Path / ServiceAccount): aplicável somente às alterações de serviço Windows |
| SvcDisplayName |ConfigurationChange |Nome de exibição do serviço que foi alterado |
| SvcName |ConfigurationChange |Nome do serviço que foi alterado |
| SvcState |ConfigurationChange |Novo (atual) estado do serviço |
| SvcPreviousState |ConfigurationChange |Estado anterior conhecido do serviço (aplicável somente se o estado do serviço for alterado) |
| SvcStartupType |ConfigurationChange |Tipo de inicialização do serviço |
| SvcPreviousStartupType |ConfigurationChange |Tipo anterior de inicialização do serviço (aplicável somente se o tipo de inicialização de serviço for alterado) |
| SvcAccount |ConfigurationChange |Conta de serviço |
| SvcPreviousAccount |ConfigurationChange |Conta de serviço anterior (aplicável somente se a conta de serviço for alterada) |
| SvcPath |ConfigurationChange |Caminho do executável do serviço Windows |
| SvcPreviousPath |ConfigurationChange |Caminho anterior do executável para o serviço do Windows (aplicável somente se ele for alterado) |
| SvcDescription |ConfigurationChange |Descrição do serviço |
| Voltar |ConfigurationChange |Estado anterior do software (versão instalada/não instalada/atual) |
| Atual |ConfigurationChange |Estado mais recente do software (versão instalada/não instalada/atual) |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre pesquisas de log:

* Familiarize-se com as [pesquisas de log](log-analytics-log-searches.md) para exibir informações detalhadas reunidas por soluções.
* Use [Campos personalizados no Log Analytics](log-analytics-custom-fields.md) para estender as pesquisas de log.

<!--HONumber=Oct16_HO2-->


