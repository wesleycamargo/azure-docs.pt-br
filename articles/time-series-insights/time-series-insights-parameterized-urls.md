---
title: "Compartilhar exibições personalizadas do Azure Time Series Insights com URLs parametrizadas | Microsoft Docs"
description: "Este artigo descreve como desenvolver URLs parametrizadas no Azure Time Series Insights para que uma exibição do cliente possa ser facilmente compartilhada."
services: time-series-insights
ms.service: time-series-insights
author: kfile
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/15/2017
ms.openlocfilehash: d7a4401ff87a94265c588aa5ef070bdc8544fe12
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Compartilhar uma exibição personalizada usando uma URL parametrizada

Para compartilhar uma exibição personalizada no explorador do Time Series Insights, você pode criar programaticamente uma URL parametrizada da exibição personalizada.

O explorador do Time Series Insights dá suporte aos parâmetros de consulta de URL para exibições específicas na experiência diretamente da URL.  Por exemplo, usando apenas a URL, você pode especificar um ambiente de destino, um predicado de pesquisa e um período de tempo desejado. Quando um usuário clica na URL personalizada, a interface fornece um link diretamente para esse ativo no portal do Time Series Insights.  Aplicam-se políticas de acesso a dados. 

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica a ID do ambiente de destino.  Este é um componente do FQDN de acesso a dados, e pode ser encontrado no canto superior direito da visão geral do ambiente no portal do Azure.  É tudo o que precede `env.timeseries.azure.com`. Um parâmetro de ID do ambiente de exemplo é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Você pode especificar valores de tempo relativos ou absolutos com uma URL parametrizada.

### <a name="absolute-time-values"></a>Valores de tempo absolutos

Para valores de tempo absolutos, use os parâmetros `from=<integer>` e `to=<integer>`. 

`from=<integer>` é um valor em milissegundos de JavaScript da hora de início para o alcance de pesquisa.

`to=<integer>` é um valor em milissegundos de JavaScript da hora de término para o alcance de pesquisa. 

Para identificar os milissegundos de JavaScript para uma data, veja [Conversor de carimbo de data/hora Epoch & Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de tempo relativos

Para um valor de tempo relativo, use `relativeMillis=<value>`, onde *value* está em milissegundos de JavaScript dos dados mais recentes no back-end.

Por exemplo, `&relativeMillis=3600000` exibe os 60 minutos mais recentes dos dados.

Os valores aceitos correspondem ao menu **rápida** do explorador do Time Series Insights e incluem o seguinte:

- 1800000 (Últimos 30 minutos)
- 3600000 (Últimos 60 minutos)
- 10800000 (Últimas 3 Horas)
- 21600000 (Últimas 6 Horas)
- 43200000 (Últimas 12 Horas)
- 86400000 (Últimas 24 Horas)
- 604800000 (Últimos 7 Dias)
- 2592000000 (Últimas 30 Horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O parâmetro `timeSeriesDefinitions=<collection of term objects>` especifica os termos de uma exibição do Time Series Insights, onde:

- `name=<string>`
  - O nome do *termo*.
- `splitBy=<string>`
  - O nome da coluna para *dividido por*.
- `measureName=<string>`
  - O nome da coluna de *medida*.
- `predicate=<string>`
  - O cláusula *where* para filtragem do lado do servidor.

 
### <a name="examples"></a>Exemplos

Por exemplo, para adicionar as definições de série de tempo como um parâmetro de URL, você pode usar o seguinte:

```
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Uso destas definições de série de tempo de exemplo para 

- ID do ambiente
- últimos 60 minutos de dados
- termos (F1PressureID, F2TempStation e F3VibrationPL) que incluem os parâmetros opcionais
 
você pode construir a URL parametrizada a seguir para uma exibição:

`https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]`

Se tiver usado o explorador do Time Series Insights para criar a exibição descrita na URL anterior, ela terá esta aparência:

![Termos do explorador do Time Series Insights](media/parameterized-url/url1.png)

A exibição completa (incluindo o gráfico) teria esta aparência:

![Modo de exibição de Gráfico](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Próximas etapas
[Consultar dados usando C#](time-series-insights-query-data-csharp.md)
