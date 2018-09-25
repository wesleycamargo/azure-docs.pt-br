---
title: Trabalhar com valores de data/hora em consultas do Azure Log Analytics | Microsoft Docs
description: Descreve como trabalhar com dados de data e hora em consultas do Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 9b0c58fdbfb0d55b3b8998f4edfc1222b9a3d4aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988590"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Trabalhar com valores de data/hora em consultas do Log Analytics

> [!NOTE]
> Você deve concluir a [Introdução ao portal do Analytics](get-started-analytics-portal.md) e [Introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas do Log Analytics.


## <a name="date-time-basics"></a>Noções básicas de data/hora
A linguagem de consulta do Log Analytics tem dois tipos de dados principais associados a datas e horas: datetime e timespan. Todas as datas são expressas em UTC. Embora haja suporte para vários formatos de datetime, o formato ISO8601 é preferencial. 

Intervalos de tempo (timespans) são expressos como um decimal seguido por uma unidade de tempo:

|abreviação   | unidade de tempo    |
|:---|:---|
|d           | dia          |
|h           | hora         |
|m           | minuto       |
|s           | segundo       |
|ms          | milissegundo  |
|microssegundo | microssegundo  |
|tique        | nanossegundo   |

Datetimes pode ser criados ao converter uma cadeia de caracteres usando o operador `todatetime`. Por exemplo, para examinar as pulsações da VM enviadas em um período de tempo específico, você pode fazer uso do [operador intermediário](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator) que é conveniente para especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar uma datetime à data e hora atual. Por exemplo, para ver todas as pulsações durante os últimos dois minutos, você pode usar o operador `now` junto com um timespan que representa dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Um atalho também está disponível para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

No entanto o método mais curto e mais legível é usar o operador `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Suponha que, em vez de saber a hora de início e de término, você sabe a hora de início e a duração. É possível reescrever a consulta da seguinte maneira:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Converter unidades de tempo
Isso pode ser útil para expressar uma datetime ou timespan em uma unidade de tempo diferente do padrão. Por exemplo, suponha que você está analisando os eventos de erro dos últimos 30 minutos e precisa de uma coluna calculada que mostre há quanto tempo o evento ocorreu:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Você pode ver que a coluna _timeAgo_ tem valores como: "00:09:31.5118992", significando que eles estão formatados como hh:mm:ss.fffffff. Se você quiser formatar esses valores para o _numver_ de minutos decorridos desde a hora de início, basta dividir esse valor por "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregações e particionamento por intervalos de tempo
Outro cenário muito comum é a necessidade de obter estatísticas durante um certo período de tempo em um intervalo de agregação específico. Para isso, um operador `bin` pode ser usado como parte de uma cláusula de resumo.

Use a consulta a seguir para obter o número de eventos que ocorreram a cada 5 minutos durante a última meia hora:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Isso produz a seguinte tabela:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Outra maneira de criar buckets de resultados é usar funções, tais como `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Isso produz os seguintes resultados:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5.416  |


## <a name="time-zones"></a>Fusos horários
Uma vez que todos os valores de datetime são expressos em UTC, costuma ser útil convertê-los para o fuso horário local. Por exemplo, use este cálculo para converter horas UTC em horas PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [todatetime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())  [totimespan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| Valor arredondado para o tamanho do compartimento | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Obter uma data ou hora específica | [ago](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [now](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Obter parte do valor | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Obter uma data relativa ao valor  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a linguagem de consulta do Log Analytics:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Gráficos](charts.md)