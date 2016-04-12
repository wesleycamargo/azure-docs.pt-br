<properties 
	pageTitle="Resumo e agregação na Análise do Application Insights" 
	description="Referência das funções de agregação e a instrução de resumo na Análise, a ferramenta de pesquisa avançada do Application Insights." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Agregação na Análise

A [Análise](app-analytics.md) permite executar consultas avançadas na telemetria de seu aplicativo coletada pelo [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]


## operador summarize

Produz uma tabela que agrega o conteúdo da tabela de entrada.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-analytics-aggregations/01.png)

* Os registros de entrada são coletados em grupos, para que cada grupo possua uma combinação específica de valores nos campos `by`.
* As expressões de agregação são avaliadas nos membros de cada grupo.
* Há uma linha de saída para cada combinação distinta de valores das expressões 'by'. 
* Há uma coluna de saída para cada expressão de agregação e para cada expressão 'by'. Todas as outras colunas de entrada são descartadas.

### Sintaxe

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**Argumentos**

* *Column:* nome opcional para uma coluna de resultados. Assume o padrão de um nome derivado da expressão.
* *Aggregation*: uma chamada para uma função de agregação, como `count()` ou `avg()`, com nomes de coluna como argumentos. Veja a lista de funções de agregação abaixo.
* *GroupExpression:* uma expressão sobre as colunas que fornece um conjunto de valores distintos. Normalmente, é um nome de coluna que já fornece um conjunto restrito de valores ou `bin()` com uma coluna numérica ou de hora como argumento. 

Se você fornecer uma expressão numérica ou de hora sem usar `bin()`, a Análise a aplicará automaticamente com um intervalo de `1h` para horas ou de `1.0` para números.

Se você não fornecer um *GroupExpression*, toda a tabela será resumida em uma única linha de saída.

Você deve usar um tipo simples, não é um tipo dinâmico, na cláusula `by`. Por exemplo, aqui a conversão `tostring` é essencial:

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### Resumir por colunas com valores discretos

Consulte para mostrar os tempos médios de resposta para solicitações HTTP diferentes, separando quaisquer solicitações com códigos de resposta diferentes:

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-analytics-aggregations/03.png)


* As expressões de agregação (como count e avg) precisam ser formadas a partir de funções de agregação documentadas neste artigo. Seus argumentos podem ser quaisquer funções escalares.
* As expressões de agrupamento (após 'by') podem ser qualquer expressão escalar, mas serão mais eficazes se forem apenas nomes de campo.

### Resumir por colunas numéricas

Se quisermos agrupar por um escalar contínuo, como um número ou hora, precisamos usar a função `bin` (também conhecida como `floor`) para dividir o intervalo contínuo em compartimentos.

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-analytics-aggregations/04.png)

(O campo Duração da solicitação é um número em milissegundos.)
 
## Dicas

* Use `where` para remover todas as linhas indesejadas antes de `summarize`.
 * Remova os valores nulos. Um valor nulo em um grupo tornará o resultado da agregação nulo. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* Embora você possa fornecer expressões aleatórias para as expressões de agregação e de agrupamento, é mais eficiente usar nomes de campo simples ou aplicar `bin()` a um campo numérico.





## Exemplos

Localize o carimbo de hora mínimo e máximo de todos os registros na tabela Atividades. Não há qualquer cláusula group-by e, portanto, há apenas uma linha na saída:

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`



#### Duração da sessão

As sessões em um log de eventos têm vários eventos. Localize o início e o término de cada sessão procurando os eventos mais recentes e mais antigos em cada sessão:

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

A operação `extend` adiciona a coluna de duração, usando `bin` (também conhecido como `floor`) para arredondar para o segundo mais próximo.

![](./media/app-analytics-aggregations/minmax.png)

#### Exemplo: duração média

Agora, vamos localizar as durações médias de sessão para clientes em cidades diferentes:

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

Adicionamos a coluna `client_City` à cláusula `by` para que ela passe pela primeira operação de resumo. Supondo que todos os eventos de uma sessão de cliente ocorram na mesma cidade, isso não acrescentará ao número de saídas do resumo.


![](./media/app-analytics-aggregations/durationcity.png)


#### Exemplo: 

Encontre o horário mais ocupado do dia em cada cidade do cliente. Por 'mais ocupado', queremos dizer a hora do dia na qual a quantidade máxima de sessões é iniciada em um dia comum.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## AGREGAÇÕES

## qualquer 

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
## argmin, argmax

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


![](./media/app-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

Calcula a média da *Expressão* no grupo.

## buildschema

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

#### Modelo de esquema

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


## count

    count([ Predicate ])

Retorna uma contagem de linhas para o qual *Predicado* é avaliado como `true`. Se nenhum *Predicado* for especificado, retorna o número total de registros no grupo.

**Dica de desempenho**: use `summarize count(filter)` em vez de `where filter | summarize count()`
   

## dcount

    dcount( Expression [ ,  Accuracy ])

Retorna uma estimativa do número de valores distintos de *Expr* no grupo. (Para listar os valores distintos, use [`makeset`](#makeset)).

*Precisão*, se for especificada, controla o equilíbrio entre velocidade e precisão.

 * `0` = o cálculo menos preciso e mais rápido.
 * `1`, que é o padrão, equilibra a precisão e o tempo de cálculo; cerca de 0,8% de erro.
 * `2` = cálculo mais preciso e mais lento; cerca de 0,4% de erro.

**Exemplo**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

Retorna uma matriz `dynamic` (JSON) de todos os valores de *Expr* no grupo.

* *MaxListSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é *128*).

## makeset

    makeset(Expression [ , MaxSetSize ] )

Retorna uma matriz `dynamic` (JSON) do conjunto de valores distintos que *Expr* usa no grupo. (Dica: para contar apenas os valores distintos, use [`dcount`](#dcount)).
  
*  *MaxSetSize* é um limite de inteiro opcional sobre o número máximo de elementos retornados (o padrão é *128*).

**Exemplo**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/makeset.png)

Consulte também o operador [`mvexpand`](app-analytics-queries.md#mvexpand-operator) para a função oposta.


## max, min

    max(Expr)

Calcula o número máximo de *Expr*.
    
    min(Expr)

Calcula o número mínimo de *Expr*.

**Dica**: isso oferece a você o mínimo ou o máximo por conta própria, por exemplo, o preço mais alto ou o mais baixo. Mas se você quiser outras colunas na linha, por exemplo, o nome do fornecedor com o menor preço, use [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
## percentile, percentiles

    percentile(Expression, Percentile)

Retorna uma estimativa para a *Expressão* do percentual especificado no grupo. A precisão depende da densidade da população na região do percentil.
    
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

![](./media/app-analytics-aggregations/percentiles.png)

Os resultados mostram que para a solicitação /Events/Index, 5% das solicitações recebem respostas em menos de 2,44 s, metade delas em 3,52 s, e 5% são mais lentas do que 6,85 s.


Calcule várias estatísticas:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### Erro de estimativa em percentuais

As agregação de percentis fornece um valor aproximado usando [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Alguns pontos importantes:

* Os limites no erro de estimativa variam de acordo percentil solicitado. A maior precisão está nas extremidades da escala de [0 a 100], os percentuais 0 e 100 são os valores mínimo e máximo exatos da distribuição. A precisão diminui gradativamente rumo à parte central da escala. Ela atinge seu pior valor na mediana e está limitada a 1%. 
* Os limites de erro são observados na classificação, não no valor. Suponha que percentil (X, 50) retornou o valor de Xm. A estimativa garante que pelo menos 49% e, no máximo, 51% dos valores de X sejam inferiores Xm. Não há qualquer limite teórico sobre a diferença entre Xm e o valor real da mediana de X.

## stdev

     stdev(Expr)

Retorna o desvio padrão de *Expr* no grupo.

## variance

    variance(Expr)

Retorna a variação de *Expr* no grupo.

## sum

    sum(Expr)

Retorna a soma de *Expr* no grupo.




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!-----------HONumber=AcomDC_0330_2016-->