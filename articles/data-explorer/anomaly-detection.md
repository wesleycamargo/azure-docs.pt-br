---
title: Detecção de anomalias de série temporal e previsões no Data Explorer do Azure
description: Saiba como analisar dados de série temporal para detecção de anomalias e previsão usando o Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872001"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detecção de anomalias e previsões no Data Explorer do Azure

Gerenciador de dados do Azure executa coleta contínua de dados de telemetria de dispositivos de IoT ou serviços de nuvem. Esses dados são analisados para diversos insights, como o monitoramento de integridade do serviço, os processos de produção físicas, as tendências de uso e previsão de carga. A análise é feita na série de tempo de métricas selecionadas para localizar um padrão de desvio da métrica em relação ao seu padrão de linha de base normal típico. O Gerenciador de dados do Azure contém suporte nativo para a criação, manipulação e análise de várias séries de tempo. Ele pode criar e analisar a milhares de série de tempo em segundos, habilitando quase fluxos de trabalho e soluções de monitoramento em tempo real.

Este artigo detalha as Data Explorer do Azure série anomalias detecção e previsão de capacidades de tempo. As funções de série de tempo aplicável baseiam-se em um modelo robusto Decomposição bem conhecidos, onde cada série temporal original é decomposto em tendência sazonal, e os componentes residuais. Anomalias são detectadas pelo exceções no componente residual, enquanto a previsão é feita pelo extrapolar o sazonais e componentes de tendência. A implementação do Gerenciador de dados do Azure aprimora significativamente o modelo básico de Decomposição pela detecção automática de sazonalidade, análise de exceções robusto e implementação vetorizada para processar milhares de série de tempo em segundos.

## <a name="prerequisites"></a>Pré-requisitos

Leia [tempo de análise de séries no Gerenciador de dados do Azure](/azure/data-explorer/time-series-analysis) para uma visão geral dos recursos da série de tempo.

## <a name="time-series-decomposition-model"></a>Modelo de Decomposição de série temporal

Uma implementação nativa do Data Explorer do Azure para previsão de série temporal e detecção de anomalias usa um modelo de Decomposição bem conhecidos. Esse modelo é aplicado a série de tempo das métricas esperadas periódicas e comportamento de tendência, como o tráfego do serviço, pulsações de componente e medidas periódicas de IoT para prever valores futuros de métrica e detectar aqueles anormais de manifesto. A suposição de que esse processo de regressão é que o que não seja previamente conhecido sazonal e o comportamento de tendência, o tempo de série é distribuída aleatoriamente. Em seguida, você pode prever valores futuros de métrica do sazonais e componentes de tendência, coletivamente chamado de linha de base e ignorar a parte restante. Você também pode detectar valores anormais com base na análise de exceções usando somente a parte restante.
Para criar um modelo de Decomposição, use a função [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). O `series_decompose()` função usa um conjunto de série temporal e automaticamente decompõe cada série temporal para seu sazonais, tendência, residual e componentes de linha de base. 

Por exemplo, você pode decompor o tráfego de um serviço web interno usando a consulta a seguir:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Decomposição da série temporal](media/anomaly-detection/series-decompose-timechart.png)

* A série temporal original é rotulada **num** (em vermelho). 
* O processo é iniciado pela detecção automática de sazonalidade, usando a função [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) e extrai o **sazonais** padrão (em roxo).
* O padrão sazonal é subtraído de série temporal original e uma regressão linear é executada usando a função [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) para localizar o **tendência** componente (em azul claro).
* A função subtrairá a tendência e o restante é o **residual** componente (em verde).
* Por fim, a função adiciona a temporada de tendências e componentes para gerar a **linha de base** (em azul).

## <a name="time-series-anomaly-detection"></a>Detecção de anomalias de série temporal

A função [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) localiza pontos anormais em um conjunto de série temporal. Essa função chama `series_decompose()` para criar o modelo de Decomposição e, em seguida, executa [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) no componente residual. `series_outliers()` calcula as pontuações de anomalias para cada ponto do componente residual usando teste de limite do Tukey. Pontuações de anomalias acima 1.5 ou abaixo de -1.5 indicam um aumento de anomalias leve ou recusar, respectivamente. Pontuações de anomalias acima 3.0 ou abaixo de-3,0. indicam uma anomalia de alta segurança. 

A consulta a seguir permite detectar anomalias no tráfego de serviço interno da web:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Detecção de anomalias de série temporal](media/anomaly-detection/series-anomaly-detection.png)

* A série original de tempo (em vermelho). 
* A linha de base (sazonais + tendência) componente (em azul).
* Os pontos anormais (em roxo) na parte superior da série temporal original. Os pontos anormais significativamente desviar-se os valores de linha de base esperada.

## <a name="time-series-forecasting"></a>Previsão de série temporal

A função [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) prevê valores futuros de um conjunto de série temporal. Essa função chama `series_decompose()` criar a modelo e, então, para cada série temporal, a decomposição extrapola o componente de linha de base para o futuro.

A consulta a seguir permite prever o tráfego do serviço web da próxima semana:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Previsão de série temporal](media/anomaly-detection/series-forecasting.png)

* Métrica original (em vermelho). Valores futuros estão ausentes e definido como 0, por padrão.
* Extrapole o componente de linha de base (em azul) para prever valores da próxima semana.

## <a name="scalability"></a>Escalabilidade

Sintaxe de linguagem de consulta de Data Explorer do Azure permite que uma única chamada processar várias séries Temporais. Sua implementação otimizada exclusiva permite um desempenho rápido, que é essencial para a detecção de anomalias em vigor e previsão ao monitorar milhares de contadores no próximo cenários em tempo real.

A consulta a seguir mostra o processamento de três séries de tempo simultaneamente:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Escalabilidade de série de tempo](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Resumo

Este documento detalha as funções nativas do Data Explorer do Azure para detecção de anomalias de série temporal e previsão, que melhora significativamente o modelo de Decomposição básico no qual ele se baseia. Cada série temporal original é decomposto, anomalias detectadas, e previsão executada. As funcionalidades de detecção e previsão de anomalias do série tempo são usadas para perto de cenários de monitoramento em tempo real, como detecção de falhas, manutenção preditiva e por demanda e previsão de carga.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [recursos de aprendizado de máquina](/azure/data-explorer/machine-learning-clustering) no Gerenciador de dados do Azure.