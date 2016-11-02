<properties 
    pageTitle="Um tour pelo Analytics no Application Insights | Microsoft Azure" 
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
    ms.date="10/03/2016" 
    ms.author="awills"/>


 

# <a name="a-tour-of-analytics-in-application-insights"></a>Um tour pela Análise no Application Insights


A [Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.


* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não está enviando dados para o Application Insights.


Vamos analisar algumas consultas básicas para começar.

## <a name="connect-to-your-application-insights-data"></a>Conectar-se aos dados do Application Insights

Abra o Analytics na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics-tour/001.png)

    
## <a name="[take](app-insights-analytics-reference.md#take-operator):-show-me-n-rows"></a>[Take](app-insights-analytics-reference.md#take-operator): mostre-me n linhas

Os pontos de dados que registram em log operações de usuário (geralmente solicitações HTTP recebidas pelo seu aplicativo Web) são armazenados em uma tabela chamada `requests`. Cada linha é um ponto de dados de telemetria recebido do SDK do Application Insights em seu aplicativo.

Vamos começar examinando algumas linhas de exemplo da tabela:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Coloque o cursor em algum lugar na instrução antes de clicar em Ir. Você pode dividir uma instrução em mais de uma linha, mas não coloque linhas em branco em uma instrução. As linhas em branco são uma maneira conveniente de manter várias consultas separadas na janela.


Escolha as colunas e ajuste as posições:

![Clique na seleção de coluna no canto superior direito dos resultados](./media/app-insights-analytics-tour/030.png)


Expanda algum item para ver os detalhes:
 
![Escolha Tabela e use Configurar Colunas](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Clique no cabeçalho de uma coluna para reordenar os resultados disponíveis no navegador da Web. Mas lembre-se de que, para um conjunto de resultados grande, o número de linhas baixadas para o navegador é limitado. Esteja ciente de que classificar dessa maneira nem sempre mostra os reais itens mais altos ou mais baixos. Para isso, você deve usar o operador `top` ou `sort`. 

## <a name="[top](app-insights-analytics-reference.md#top-operator)-and-[sort](app-insights-analytics-reference.md#sort-operator)"></a>[Superior](app-insights-analytics-reference.md#top-operator) e [classificação](app-insights-analytics-reference.md#sort-operator)

`take` é útil para obter um exemplo rápido de um resultado, mas mostra linhas da tabela sem uma ordem específica. Para obter uma exibição ordenada, use `top` (para obter um exemplo) ou `sort` (na tabela inteira).

Mostre-me as primeiras n linhas, ordenadas por uma coluna específica:

```AIQL

    requests | top 10 by timestamp desc 
```

* *Sintaxe:* a maioria dos operadores têm parâmetros de palavra-chave, como `by`.
* `desc` = ordem decrescente, `asc` = ordem crescente.

![](./media/app-insights-analytics-tour/260.png)

`top...` é uma maneira mais eficaz de dizer `sort ... | take...`. Poderíamos ter escrito:

```AIQL

    requests | sort by timestamp desc | take 10
```

O resultado seria o mesmo, mas ele seria executado um pouco mais lentamente. (Também é possível escrever `order`, que é um alias de `sort`.)

Os cabeçalhos de coluna no modo de exibição de tabela também podem ser usados para classificar os resultados na tela. Mas, obviamente, se você usou `take` ou `top` para recuperar apenas parte de uma tabela, apenas os registros que foram recuperados serão reordenados.


## <a name="[project](app-insights-analytics-reference.md#project-operator):-select,-rename-and-compute-columns"></a>[Project](app-insights-analytics-reference.md#project-operator): selecionar, renomear e computar colunas

Use [`project`](app-insights-analytics-reference.md#project-operator) para selecionar apenas as colunas desejadas:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


Você também pode renomear e definir novas colunas:

```AIQL

    requests 
  	| top 10 by timestamp desc 
  	| project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Os [nomes de coluna](app-insights-analytics-reference.md#names) poderão incluir espaços ou símbolos se eles estiverem entre colchetes, desta forma: `['...']` ou `["..."]`
* `%` é o operador de módulo normal. 
* `1d` (que é o dígito um e um “d”) é um literal de timespan que significa um dia. Aqui estão mais alguns literais de timespan: `12h`, `30m`, `10s` e `0.01s`.
* `floor` (alias `bin`) arredonda um valor até o múltiplo mais próximo do valor de base fornecido. De modo que `floor(aTime, 1s)` arredonda um tempo até o segundo mais próximo.

As [expressões](app-insights-analytics-reference.md#scalars) podem incluir todos os operadores comuns (`+`, `-`, ...) e há uma variedade de funções úteis.

    

## <a name="[extend](app-insights-analytics-reference.md#extend-operator):-compute-columns"></a>[Extend](app-insights-analytics-reference.md#extend-operator): computar colunas

Se quiser apenas adicionar colunas às existentes, use [`extend`](app-insights-analytics-reference.md#extend-operator):

```AIQL

    requests 
  	| top 10 by timestamp desc
  	| extend timeOfDay = floor(timestamp % 1d, 1s)
```

O uso de [`extend`](app-insights-analytics-reference.md#extend-operator) será menos detalhado do que o de [`project`](app-insights-analytics-reference.md#project-operator) se você quiser manter todas as colunas existentes.


## <a name="[summarize](app-insights-analytics-reference.md#summarize-operator):-aggregate-groups-of-rows"></a>[Resumir](app-insights-analytics-reference.md#summarize-operator): agregar grupos de linhas

`Summarize` aplica uma *função de agregação* especificada em grupos de linhas. 

Por exemplo, o tempo que o seu aplicativo Web leva para responder a uma solicitação é informado no campo `duration`. Vamos ver o tempo médio de resposta para todas as solicitações:

![](./media/app-insights-analytics-tour/410.png)

Ou podemos separar o resultado em solicitações de nomes diferentes:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` coleta os pontos de dados no fluxo dos grupos para os quais a cláusula `by` é igualmente avaliada. Cada valor na expressão `by` (cada nome de operação no exemplo acima) resulta em uma linha na tabela de resultados. 

Ou podemos agrupar os resultados por hora do dia:

![](./media/app-insights-analytics-tour/430.png)

Observe como estamos usando a função `bin` (também conhecida como `floor`). Se usássemos apenas `by timestamp`, cada linha de entrada acabaria em seu próprio pequeno grupo. Para qualquer escalar contínuo, como horas ou números, temos que dividir o intervalo contínuo em um número gerenciável de valores discretos; e `bin` - que é apenas a conhecida função de arredondamento para baixo `floor` - é a maneira mais fácil de fazer isso.

Podemos usar a mesma técnica para reduzir intervalos de cadeias de caracteres:


![](./media/app-insights-analytics-tour/440.png)

Observe que você pode usar `name=` para definir o nome de uma coluna de resultado, seja nas expressões de agregação, seja na cláusula by.

## <a name="counting-sampled-data"></a>Contando dados de amostra

`sum(itemCount)` é a agregação recomendada para contar eventos. Em muitos casos, itemCount==1, de modo que a função simplesmente conta até o número de linhas no grupo. Mas quando a [amostragem](app-insights-sampling.md) estiver em operação, apenas uma fração dos eventos originais será retida como pontos de dados no Application Insights, de modo que para cada ponto de dados que você visualizar, haverá `itemCount` eventos. 

Por exemplo, se a amostragem descartar 75% dos eventos originais, itemCount == 4 nos registros retidos - ou seja, para cada registro retido, houve quatro registros originais. 

A amostragem adaptável fará com que itemCount seja maior durante períodos em que seu aplicativo estiver sendo muito usado.

Portanto, a soma de itemCount dá uma boa estimativa do número original de eventos.


![](./media/app-insights-analytics-tour/510.png)

Também há uma agregação `count()` (e uma operação de contagem), para casos em que você realmente quiser contar o número de linhas em um grupo.


Há uma série de [funções de agregação](app-insights-analytics-reference.md#aggregations).


## <a name="charting-the-results"></a>Colocando os resultados em gráficos


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

Por padrão, os resultados são exibidos como uma tabela:

![](./media/app-insights-analytics-tour/225.png)


Podemos fazer melhor do que a exibição de tabela. Vamos examinar os resultados no modo de exibição de gráfico com a opção de barra vertical:

![Clique em Gráfico, escolha o gráfico de barras Vertical e atribua os eixos x e y](./media/app-insights-analytics-tour/230.png)

Observe que, embora não tenhamos classificado os resultados por tempo (como você pode ver na exibição de tabela), a exibição de gráfico sempre mostra datetimes na ordem correta.


## <a name="[where](app-insights-analytics-reference.md#where-operator):-filtering-on-a-condition"></a>[Where](app-insights-analytics-reference.md#where-operator): filtragem de uma condição

Se você tiver configurado o monitoramento do Application Insights para os lados do [cliente](app-insights-javascript.md) e do servidor de seu aplicativo, alguma telemetria do banco de dados virá de navegadores.

Vamos ver apenas exceções relatadas pelos navegadores:

```AIQL

    exceptions 
  	| where device_Id == "browser" 
  	|  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

O operador `where` usa uma expressão booliana. Eis alguns pontos importantes sobre eles:

 * `and`, `or`: Operadores booleanos
 * `==`, `<>`: igual a e diferente de
 * `=~`, `!=`: cadeia de caracteres que não diferencia maiúsculas de minúsculas "igual a" e "diferente de". Há muitos outros operadores de comparação de cadeia de caracteres.

Leia tudo sobre [expressões escalares](app-insights-analytics-reference.md#scalars).

### <a name="filtering-events"></a>Filtragem de eventos

Solicitações malsucedidas de localização:

```AIQL

    requests 
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` tem o tipo string, portanto devemos [convertê-lo](app-insights-analytics-reference.md#casts) em uma comparação numérica.

Resuma as diferentes respostas:

```AIQL

    requests
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
  	| summarize count() 
      by resultCode
```

## <a name="timecharts"></a>Timecharts

Mostra quantos eventos existem por dia:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Selecione a opção de exibição Gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

O eixo x para gráficos de linhas tem que ser do tipo DateTime. 

## <a name="multiple-series"></a>Várias séries 

Use vários valores em uma cláusula `summarize by` a fim de criar uma linha separada para cada combinação de valores:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Para exibir várias linhas em um gráfico, clique em **Dividir por** e escolha uma coluna.

![](./media/app-insights-analytics-tour/100.png)



## <a name="daily-average-cycle"></a>Ciclo médio diário

Como o uso varia na média diária?

Conte solicitações pelo módulo de tempo de um dia, compartimentalizado por horas:

```AIQL

    requests
  	| extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
  	| summarize event_count=count() by hour
```

![Gráfico de linhas de horas em um dia normal](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Observe que, no momento, temos que converter durações de tempo em datas/horas para exibir em um gráfico.


## <a name="compare-multiple-daily-series"></a>Comparar várias séries diárias

Como o uso varia de acordo com a hora do dia em diferentes estados?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Divida o gráfico por estado:

![Dividir por client_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## <a name="plot-a-distribution"></a>Plotar uma distribuição

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


![](./media/app-insights-analytics-tour/290.png)



## <a name="[percentiles](app-insights-analytics-reference.md#percentiles)"></a>[Percentis](app-insights-analytics-reference.md#percentiles)

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

![result](./media/app-insights-analytics-tour/180.png)

O que nos mostra que:

* 5% das sessões têm uma duração de menos de 3 minutos e 34 segundos; 
* 50% das sessões duram menos de 36 minutos;
* 5% das sessões duram mais de 7 dias

Para obter uma análise separada para cada país, temos apenas que trazer a coluna client_CountryOrRegion para ambos os operadores summarize:

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

![](./media/app-insights-analytics-tour/190.png)


## <a name="[join](app-insights-analytics-reference.md#join)"></a>[Join](app-insights-analytics-reference.md#join)

Temos acesso a várias tabelas, incluindo solicitações e exceções.

Para encontrar as exceções relacionadas a uma solicitação que retornou uma resposta com falha, podemos unir as tabelas em `session_Id`:

```AIQL

    requests 
  	| where toint(responseCode) >= 500 
  	| join (exceptions) on operation_Id 
  	| take 30
```


É uma prática recomendável usar `project` para selecionar apenas as colunas necessárias antes de executar a junção.
Renomeamos a coluna de carimbo de data/hora nas mesmas cláusulas.



## <a name="[let](app-insights-analytics-reference.md#let-clause):-assign-a-result-to-a-variable"></a>[Let](app-insights-analytics-reference.md#let-clause): atribuir um resultado a uma variável

Use [let](./app-insights-analytics-reference.md#let-statements) para separar as partes da expressão anterior. Os resultados não mudam:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
  	| join (exceptions) on session_Id 
  	| take 30
```

> Dica: no cliente da Análise, não coloque linhas em branco entre as partes dele. Execute tudo.


## <a name="accessing-nested-objects"></a>Acessando objetos aninhados

Os objetos aninhados podem ser acessados facilmente. Por exemplo, no fluxo de exceções, você verá objetos estruturados como este:

![result](./media/app-insights-analytics-tour/520.png)

Você pode mesclá-lo escolhendo as propriedades que te interessam:

```AIQL

    exceptions | take 10
  	| extend method1 = tostring(details[0].parsedStack[1].method)
```

Observe que você precisa usar uma [conversão](app-insights-analytics-reference.md#casts) para o tipo apropriado.

## <a name="custom-properties-and-measurements"></a>Medidas e propriedades personalizadas

Se o seu aplicativo anexar [dimensões personalizadas (propriedades) e medidas personalizadas](app-insights-api-custom-events-metrics.md#properties) a eventos, você os verá nos objetos `customDimensions` e `customMeasurements`.


Por exemplo, se o seu aplicativo inclui:

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Para extrair esses valores na Análise:

```AIQL

    customEvents
  	| extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

## <a name="tables"></a>Tabelas

O fluxo de telemetria recebido de seu aplicativo é acessível por meio de várias tabelas. O esquema de propriedades disponível para cada tabela está visível na parte esquerda da janela.

### <a name="requests-table"></a>Tabela de solicitações

Conte as solicitações HTTP para seu aplicativo Web e segmento por nome de página:

![Conte solicitações segmentadas por nome](./media/app-insights-analytics-tour/analytics-count-requests.png)

Descubra as solicitações que mais falham:

![Conte solicitações segmentadas por nome](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabela de eventos personalizada

Se usar [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) para enviar seus próprios eventos, você poderá lê-los nessa tabela. 

Vejamos um exemplo em que o código do aplicativo contém estas linhas:

```C#

    telemetry.TrackEvent("Query", 
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Exiba a frequência destes eventos:
 
![Taxa de exibição de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrair medidas e dimensões de eventos:

![Taxa de exibição de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabela de métricas personalizada

Se estiver usando [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para enviar seus próprios valores de métricas, você encontrará os resultados no fluxo **customMetrics**. Por exemplo:  

![Métricas personalizadas na análise do Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)


> [AZURE.NOTE] No [Metrics Explorer](app-insights-metrics-explorer.md), todas as medidas personalizadas anexadas a qualquer tipo de telemetria aparecem juntas na folha de métricas, juntamente com métricas enviadas usando `TrackMetric()`. No Analytics, no entanto, as medidas personalizadas ainda estão conectadas a qualquer tipo de telemetria no qual foram realizadas, eventos ou solicitações, entre outros, enquanto as métricas enviadas pelo TrackMetric são exibidas em seu próprio fluxo.

### <a name="performance-counters-table"></a>Tabela de contadores de desempenho

Os [contadores de desempenho](app-insights-web-monitor-performance.md#system-performance-counters) mostram métricas básicas do sistema para seu aplicativo, tais como CPU, memória e uso de rede. Você pode configurar o SDK para enviar outros contadores, incluindo seus próprios contadores personalizados.

O esquema **performanceCounters** expõe o nome `category`, `counter` e o nome `instance` de cada contador de desempenho. Nomes de instância do contador só são aplicáveis a alguns contadores de desempenho e geralmente indicam o nome do processo ao qual a contagem está relacionada. Na telemetria de cada aplicativo, você verá apenas os contadores para aquele aplicativo. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Para obter um gráfico da memória disponível no período recente: 

![Gráfico de tempo da memória na análise do Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)


Como outras telemetrias, o **performanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade do computador host no qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em diferentes computadores: 


![Desempenho segmentado por instância de função na análise do Application Insights](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabela de exceções

As [exceções relatadas pelo seu aplicativo](app-insights-asp-net-exceptions.md) estão disponíveis nessa tabela. 

Para localizar a solicitação HTTP que seu aplicativo estava manipulando quando a exceção foi gerada, ingresse em operation_Id:

![Ingressar em exceções com solicitações em operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)


### <a name="browser-timings-table"></a>Tabela de intervalos do navegador

`browserTimings` mostra os dados de carregamento de página coletados nos navegadores de seus usuários.

[Configure seu aplicativo para a telemetria do lado do cliente](app-insights-javascript.md) para ver essas métricas. 

O esquema inclui [métricas que indicam os comprimentos de diferentes etapas do processo de carregamento de página](app-insights-javascript.md#page-load-performance). (Elas não indicam o tempo que os usuários levam para ler uma página.)  

Mostrar a popularidade de diferentes páginas e o tempo de carregamento para cada página:

![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availbility-results-table"></a>Tabela de resultados de disponibilidade

`availabilityResults` mostra os resultados de seus [testes da Web](app-insights-monitor-web-app-availability.md). Cada execução dos testes de cada local de teste é relatada separadamente. 


![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabela de dependências

Contém os resultados de chamadas que seu aplicativo fez para bancos de dados e APIs REST e outras chamadas para TrackDependency().

### <a name="traces-table"></a>Tabela de rastreamentos

Contém a telemetria enviada pelo seu aplicativo usando TrackTrace() ou [outras estruturas de registro](app-insights-asp-net-trace-logs.md).

## <a name="try-it!"></a>Experimente!

* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não está enviando dados para o Application Insights.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]





<!--HONumber=Oct16_HO2-->


