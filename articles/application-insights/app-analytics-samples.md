<properties 
	pageTitle="Exemplos de consultas no Application Insights – Análise" 
	description="Exemplos de consultas no Application Insights – Análise, a ferramenta de pesquisa avançada do Application Insights." 
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


# Amostras da Análise para o Application Insights

A [Análise](app-analytics.md) é um mecanismo de pesquisa avançado para sua telemetria do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise. Também há um [tour pela linguagem](app-analytics-tour.md), que recomendamos para o guia de Introdução.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Práticas recomendadas de consulta 

Há várias regras que você pode seguir para fazer com que sua consulta seja executada mais rapidamente.

FAÇA:

-	Use filtros de tempo primeiro. Application Insights – a Análise é altamente otimizada para utilizar filtros de tempo.
-	Coloque filtros que são esperados a fim de se livrar da maioria dos dados no início da consulta (logo após os filtros de tempo)
-	Verifique se a maioria dos filtros aparece no início da consulta (antes de começar a usar 'extend') 
-	Prefira a palavra-chave 'has' em vez de 'contains' ao procurar tokens completos. 'has' é mais eficaz, pois não precisa procurar subcadeias de caracteres.
-	Prefira a pesquisa em uma coluna específica em vez de usar '*' (pesquisa de texto completo em todas as colunas)
-	Ao usar o join - projete apenas as colunas necessárias de ambos os lados da junção (isso reduzirá carga transferida de um computador para outro)

EVITE:

-	Tentar novas consultas sem 'limit [número baixo]' ou 'count' no final. Executar consultas não associadas em um conjunto de dados desconhecido, pois isso pode produzir GBs de resultados que retornarão ao cliente, resultando em uma resposta lenta e tornando o cluster ocupado.
-	Se você descobrir que está aplicando conversões (JSON, string etc.) em mais de 1 bilhão de registros, remodele sua consulta para reduzir a quantidade de dados inseridos na conversão





<a name="activities"></a>
## Obter sessões de eventos start e stop

Vamos supor que temos um log de eventos, no qual alguns eventos marcam o início ou término de uma atividade ou sessão estendida.

|Nome|City|SessionId|Timestamp|
|---|---|---|---|
|Iniciar|Londres|2817330|2015-12-09T10:12:02.32|
|Jogo|Londres|2817330|2015-12-09T10:12:52.45|
|Iniciar|Manchester|4267667|2015-12-09T10:14:02.23|
|Parar|Londres|2817330|2015-12-09T10:23:43.18|
|Cancelar|Manchester|4267667|2015-12-09T10:27:26.29|
|Parar|Manchester|4267667|2015-12-09T10:28:31.72|

Cada evento tem uma SessionId, então o problema é corresponder os eventos de início e de término à mesma ID.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Use [`let`](app-analytics-syntax.md#let-statements) para nomear uma projeção da tabela que seja a mais simples possível antes de entrar na associação. [`Project`](app-analytics-queries.md#project-operator) é usado para alterar os nomes dos carimbos de data/hora, para que os horários de início e de término possam aparecer no resultado. Ele também seleciona as outras colunas que queremos ver no resultado. [`join`](app-analytics-queries.md#join-operator) faz a correspondência das entradas de início e de término para a mesma atividade, criando uma linha para cada atividade. Por fim, `project` adiciona novamente uma coluna para mostrar a duração da atividade.


|City|SessionId|StartTime|StopTime|Duração|
|---|---|---|---|---|
|Londres|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Obter sessões, sem ID da sessão

Agora vamos supor que os eventos de início e de término não tenham uma identificação de sessão para correspondência. Mas temos um endereço IP do cliente onde ocorreu a sessão. Supondo que cada endereço de cliente realize apenas uma sessão por vez, podemos fazer a correspondência de cada evento de início ao próximo evento de término do mesmo endereço IP.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

A associação corresponderá cada hora de início com todos as horas de término do endereço IP do mesmo cliente. Portanto, primeiro removemos as correspondências com as horas de término mais antigas.

Em seguida, agrupamos de acordo com a hora de início e o IP a fim de obter um grupo para cada sessão. Devemos fornecer uma função `bin` para o parâmetro StartTime: se não o fizermos, a Análise usará automaticamente as compartimentalizações de 1 hora, que corresponderão algumas horas de início com as horas de término erradas.

`argmin` seleciona a linha com a menor duração em cada grupo, e o parâmetro `*` passa por todas as outras colunas, embora use "min\_" como prefixo para cada nome de coluna.


![](./media/app-analytics-samples/040.png)

Em seguida, adicionamos um código para contar as durações nas compartimentalizações convenientemente dimensionadas. Temos uma ligeira preferência por um gráfico de barras, portanto dividimos por `1s` para converter os períodos de tempo em números.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Exemplo real

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Sessões simultâneas do gráfico ao longo do tempo

Vamos supor que tenhamos uma tabela de atividades com suas horas de início e de término. Gostaríamos de ver um gráfico ao longo do tempo que mostra quantas estão em execução simultânea a qualquer momento.

Veja uma entrada de exemplo, que chamaremos de `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Queremos um gráfico em compartimentos de um minuto, portanto, queremos criar algo que, a cada intervalo de um minuto, possamos contar para cada atividade em execução.

Veja um resultado intermediário:

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` gera uma matriz de valores nos intervalos especificados:

|SessionId | StartTime | StopTime | amostras|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Mas, em vez de manter essas matrizes, vamos [expandi-las](app-analytics-queries.md#mvexpand-operator):

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | amostras|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Agora, podemos agrupá-los pelo tempo de amostragem, contando as ocorrências de cada atividade:

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Precisamos de todatetime(), pois `mvexpand` gera uma coluna do tipo dinâmico.
* Precisamos de bin() pois, para valores numéricos e datas, summarize sempre aplica uma função bin com um intervalo padrão, se você não fornecer um. 


| count\_SessionId | amostras|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Isso pode ser processado como um gráfico de barras ou de tempo.


## Tipos de junção

O tipo exato de operador de junção é especificado com a palavra-chave kind. Até hoje, a Análise dá suporte a seis tipos de operadores de junção: junção interna com eliminação de duplicação do lado esquerdo (padrão), junção interna padrão, externa esquerda, externa direita, externa completa e antijunção esquerda.
 
Tipo de junção padrão (sem tipo especificado). Vamos usar duas tabelas de exemplo para explicar a operação da junção:
 
Tabela X


|Chave |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Tabela Y

|Chave |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
A junção padrão executa uma junção interna após a eliminação de duplicação no lado esquerdo na chave de junção (a eliminação de duplicação mantém o primeiro registro). Com base nesta instrução:

    X | join Y on Key 

o lado esquerdo efetivo da junção (tabela X após a eliminação de duplicação) seria:

|Chave |Value1 
|---|---
|a |1 
|b |2 
|c |4 

e o resultado da junção seria:

|Chave |Value1 |Chave |Value2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

(Observe que as chaves "a" e "d" não aparecem na saída, pois não havia qualquer chave correspondente em ambos os lados, esquerdo e direito).
 
(Historicamente, essa foi a primeira implementação da junção com suporte da versão inicial da Análise; ela é útil em cenários típicos de análise de log/rastreamento em que queremos correlacionar dois eventos (cada um correspondendo a algum critério de filtragem) sob a mesma ID de correlação, e obter todas as ocorrências do fenômeno que estamos procurando, ignorando várias ocorrências dos registros de rastreamento colaboradores.)
 
### Junção interna (tipo=inner) 

Essa é a junção interna padrão, como é conhecida pelo mundo SQL. O registro de saída é gerado sempre que um registro no lado esquerdo tiver a mesma chave de junção que o registro no lado direito.
 
    X | join kind=inner Y on Key 

resulta em:

|Chave |Value1 |Chave |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

Observe que (b,10) proveniente do lado direito foi ingressado duas vezes: com (b,2) e (b,3) à esquerda; além disso, (c,4) à esquerda foi ingressado duas vezes: com (c,20) e (c,30) à direita.

### Junção externa esquerda (tipo=leftouter) 

O resultado de uma junção externa esquerda para as tabelas X e Y sempre contém todos os registros da tabela esquerda (X), mesmo se a condição de junção não localizar qualquer registro correspondente na tabela direita (Y).
 
Considerando:

    X | join kind=leftouter Y on Key 

Resultado:

|Chave |Value1 |Chave |Value2 
|---|---|---|---
|a |1 |nulo |nulo 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### Junção externa direita (tipo=rightouter) 

É semelhante à junção externa esquerda, mas o tratamento das tabelas é invertido.
 
Considerando:

    X | join kind=rightouter Y on Key 

Resultado:


|Chave |Value1 |Chave |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|nulo |nulo |d |40 
 
### Junção externa completa (tipo=fullouter) 

Conceitualmente, uma junção externa completa combina o efeito da aplicação das junções externas direita e esquerda. Quando os registros nas tabelas unidas não corresponderem, o conjunto de resultados terá valores NULL para cada coluna da tabela que não possuir uma linha correspondente. Para os registros que corresponderem, uma única linha será produzida no conjunto de resultados (contendo os campos preenchidos de ambas as tabelas).
 
Considerando:

    X | join kind=fullouter Y on Key 

Resultado:

|Chave |Value1 |Chave |Value2 
|---|---|---|---
|a |1 |nulo |nulo 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|nulo |nulo |d |40 
 
### Antijunção esquerda (tipo=leftanti) 

A antijunção esquerda retorna todos os registros do lado esquerdo que não correspondem a qualquer registro no lado direito.
 
    X | join kind=leftanti Y on Key 
 
Resultado:

|Chave |Value1 
|---|---
|a |1 
 
A antijunção modela a consulta "NOT IN".



## Unir com mapeamento de dicionário embutido

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->