<properties 
	pageTitle="Um tour pela Análise no Application Insights" 
	description="Exemplos curtos de todas as principais consultas na Análise, a ferramenta de pesquisa avançada do Application Insights." 
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
	ms.date="03/24/2016" 
	ms.author="awills"/>


 
# Um tour pela Análise no Application Insights

A [Análise](app-analytics.md) permite executar consultas avançadas na telemetria de seu aplicativo coletada pelo [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Vamos analisar algumas consultas básicas para começar.

## Conectar-se aos dados do Application Insights

Abra a Análise na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-analytics/001.png)

## [Contar](app-analytics-aggregations.md#count) linhas

As métricas como os contadores de desempenho são armazenadas em uma tabela chamada métricas. Cada linha é um ponto de dados de telemetria recebido do SDK do Application Insights em um aplicativo. Para descobrir o tamanho da tabela, redirecionaremos seu conteúdo para um operador que simplesmente contará as linhas:

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Coloque o cursor em algum lugar na instrução antes de clicar em Ir. Você pode dividir uma instrução em mais de uma linha, mas não coloque linhas em branco em uma instrução. Para manter várias consultas na janela, separe-as com linhas em branco.

Eis o resultado:


![](./media/app-analytics-tour/010.png)

	
[`Count`](app-analytics-aggregations.md#count) é um dos vários [Operadores de consulta](app-analytics-queries.md) que podemos organizar em um pipe, filtrando, remodelando e unindo os dados em vários estágios.
	
## [Take](app-analytics-aggregations.md#take): mostre-me n linhas


Vamos ver alguns dados - o que há em um exemplo de cinco linhas?

```AIQL

	requests | take 5
```

Veja o que obtemos:

![results](./media/app-analytics-tour/020.png)

Escolha as colunas e ajuste as posições:

![Clique na seleção de coluna no canto superior direito dos resultados](./media/app-analytics-tour/030.png)


Expanda algum item para ver os detalhes:
 
![Escolha Tabela e use Configurar Colunas](./media/app-analytics-tour/040.png)

> [AZURE.NOTE] Clique no cabeçalho de uma coluna para reordenar os resultados disponíveis no navegador da Web. Mas lembre-se de que, para um conjunto de resultados grande, o número de linhas baixadas para o navegador é limitado. Esteja ciente de que classificar dessa maneira nem sempre mostra os reais itens mais altos ou mais baixos. Para isso, use o operador `top` ou `sort`.

## [Top](app-analytics-aggregations.md#top) e [sort](app-analytics-aggregations.md#sort)

`take` é útil para obter um exemplo rápido de um resultado, mas mostra linhas da tabela sem uma ordem específica. Para obter uma exibição ordenada, use `top` (para obter um exemplo) ou `sort` (na tabela inteira).

Mostre-me as primeiras n linhas, ordenadas por uma coluna específica:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Sintaxe:* a maioria dos operadores tem parâmetros de palavra-chave, como `by`.
* `desc` = ordem decrescente, `asc` = ordem crescente.

![](./media/app-analytics-tour/260.png)

`top...` é a maneira mais eficaz de dizer `sort ... | take...`. Poderíamos ter escrito:

```AIQL

	requests | sort by timestamp desc | take 10
```

O resultado seria o mesmo, mas ele seria executado um pouco mais lentamente. (Também é possível escrever `order`, que é um alias de `sort`).

Os cabeçalhos de coluna no modo de exibição de tabela também podem ser usados para classificar os resultados na tela. Mas, obviamente, se você usou `take` ou `top` para recuperar apenas parte de uma tabela, apenas reordenará os registros recuperados.


## [Projeto](app-analytics-aggregations.md#project): selecionar, renomear e computar colunas

Use [`project`](app-analytics-aggregations.md#project) para selecionar apenas as colunas desejadas:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


Você também pode renomear e definir novas colunas:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

Na expressão escalar:

* `%` é o operador de módulo normal. 
* `1d` (que é um dígito um e então um “d”) é um literal de timespan que significa um dia. Aqui estão mais alguns literais de timespan: `12h`, `30m`, `10s` e `0.01s`.
* O `floor` (alias `bin`) arredonda um valor até o múltiplo mais próximo do valor de base fornecido. Daí, `floor(aTime, 1s)` arredonda um tempo até o segundo mais próximo.

[Expressions](app-analytics-scalars.md) pode incluir todos os operadores comuns (`+`, `-`, ...), e há uma variedade de funções úteis.

## [Extend](app-analytics-aggregations.md#extend): computar colunas

Se quiser apenas adicionar colunas às existentes, use [`extend`](app-analytics-aggregations.md#extend):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

O uso de [`extend`](app-analytics-aggregations.md#extend) será menos detalhado do que [`project`](app-analytics-aggregations.md#project) se quiser manter todas as colunas existentes.

## [Summarize](app-analytics-aggregations.md#summarize): agregar grupos de linhas

Ao examinar um exemplo de uma tabela, podemos ver os campos onde dados de telemetria diferentes são relatados. Por exemplo, `exception | take 20` mostra rapidamente que as mensagens de exceção são relatadas em um campo chamado `outerExceptionType`.

Mas em vez de percorrer instâncias individuais, vamos perguntar quantas exceções foram relatadas de cada tipo:

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` agrupa linhas com os mesmos valores nos campos nomeados na cláusula `by`, produzindo uma única linha de resultado para cada grupo. Nesse caso, há uma linha para cada tipo de exceção. A função de agregação `count()` conta as linhas em cada grupo, fornecendo uma coluna no resultado.


Há uma variedade de [funções de agregação](app-analytics-aggregations.md) e você pode usar várias delas em um operador de resumo para produzir várias colunas computadas.

Por exemplo, vamos listar as solicitações HTTP para as quais essas exceções ocorrem. Novamente, ao inspecionar uma amostra da tabela de exceção, você verá que os caminhos da solicitação HTTP são relatados em uma coluna chamada `operation_Name`.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

A função de agregação `makeset()` cria um conjunto de todos os valores especificados em cada grupo. Como acontece neste exemplo, há apenas uma operação que dá origem a cada exceção.


O resultado de um resumo tem:

* cada coluna nomeada em `by`;
* mais uma coluna para cada expressão de agregação;
* uma linha para cada combinação de valores `by`.


## Resumir por valores escalares


Você pode usar valores escalares (numéricos, horas ou intervalos) na cláusula by. Mas os números geralmente preenchem um intervalo contínuo. Para agrupar os pontos de dados, você deverá atribuí-los a compartimentos de valores distintos. A função `bin` é útil para isto:

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` reduz os carimbos de data/hora para intervalos de um dia. É um alias de `floor`, uma função conhecida pela maioria das linguagens. Ele simplesmente reduz cada valor para o múltiplo mais próximo do módulo especificado, de modo que `summarize` possa atribuir as linhas a grupos de um tamanho lógico. (Sem ele, teríamos uma linha de resultado para cada fração separada de um segundo, o que não resumiria os dados).

Podemos fazer melhor do que o modo de exibição de tabela mostrado aqui. Vamos examinar os resultados no modo de exibição de gráfico com a opção de barra vertical:

![Clique em Gráfico, escolha o gráfico de barras Vertical e atribua os eixos x e y](./media/app-analytics-tour/230.png)

Observe que, embora não tenhamos classificado os resultados por tempo (como você pode ver na exibição de tabela), a exibição de gráfico sempre mostra datetimes na ordem correta.


## [Where](app-analytics-aggregations.md#where): filtragem de uma condição

Se você tiver configurado o monitoramento do Application Insights para os lados do [cliente](app-insights-javascript.md) e do servidor de seu aplicativo, alguma telemetria do banco de dados virá de navegadores.

Vamos ver apenas exceções relatadas pelos navegadores:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

O operador `where` usa uma expressão booliana. Eis alguns pontos importantes sobre eles:

 * `and`, `or`: operadores boolianos
 * `==`, `<>`: igual e diferente
 * `=~`, `!=`: cadeia de caracteres que não diferencia maiúsculas de minúsculas igual e diferente. Há muitos outros operadores de comparação de cadeia de caracteres.

Leia tudo sobre [expressões escalares](app-analytics-scalars.md).

### Filtragem de eventos

Solicitações malsucedidas de localização:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` tem o tipo string e, portanto, devemos [convertê-lo](app-analytics-scalars.md#casts) para uma comparação numérica.

Resuma as diferentes respostas:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Timecharts

Mostra quantos eventos existem por dia:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Selecione a opção de exibição Gráfico:

![timechart](./media/app-analytics-tour/080.png)

O eixo x para gráficos de linhas tem que ser do tipo DateTime.

## Várias séries 

Use vários valores em uma cláusula `summarize by` a fim de criar uma linha separada para cada combinação de valores:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Para exibir várias linhas em um gráfico, clique em **Dividido por** e escolha uma coluna.

![](./media/app-analytics-tour/100.png)



## Ciclo médio diário

Como o uso varia na média diária?

Conte solicitações pelo módulo de tempo de um dia, compartimentalizado por horas:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Gráfico de linhas de horas em um dia normal](./media/app-analytics-tour/120.png)

>[AZURE.NOTE] Observe que, no momento, temos que converter durações de tempo em datas/horas para exibir em um gráfico.


## Comparar várias séries diárias

Como o uso varia de acordo com a hora do dia em diferentes estados?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Divida o gráfico por estado:

![Dividir por client\_StateOrProvince](./media/app-analytics-tour/130.png)


## Plotar uma distribuição

Quantas sessões existem de comprimentos diferentes?

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

A última linha é necessária para a conversão em data/hora; atualmente, o eixo x de um gráfico de linha só pode ser datetime.

A cláusula `where` exclui sessões únicas (sessionDuration==0) e define o comprimento do eixo x.


![](./media/app-analytics-tour/290.png)



## [Percentis](app-analytics-aggregations.md#percentiles)

Quais intervalos de durações abordam diferentes porcentagens de sessões?

Use a consulta acima, mas substitua a última linha:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Também removemos o limite máximo na cláusula where para obter os números corretos, incluindo todas as sessões com mais de uma solicitação:

![result](./media/app-analytics-tour/180.png)

O que nos mostra que:

* 5% das sessões têm uma duração de menos de 3 minutos e 34 segundos; 
* 50% das sessões duram menos de 36 minutos;
* 5% das sessões duram mais de 7 dias

Para obter uma análise separada para cada país, temos apenas que trazer a coluna client\_CountryOrRegion para ambos os operadores summarize:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-analytics-tour/190.png)


## [Join](app-analytics-aggregations.md#join)

Temos acesso a várias tabelas, incluindo solicitações e exceções.

Para localizar as exceções relacionadas a uma solicitação que retornou uma resposta com falha, podemos unir as tabelas em `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


É uma prática recomendada usar `project` para selecionar apenas as colunas necessárias antes de executar a junção. Renomeamos a coluna de carimbo de data/hora nas mesmas cláusulas.



## [Let](app-analytics-aggregations.md#let): atribuir um resultado a uma variável

Use [let](./app-analytics-syntax.md#let-statements) para separar as partes da expressão anterior. Os resultados não mudam:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Dica: no cliente da Análise, não coloque linhas em branco entre as partes dele. Execute tudo.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->