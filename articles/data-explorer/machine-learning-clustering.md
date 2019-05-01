---
title: O recurso no Gerenciador de dados do Azure de aprendizado de máquina
description: Use o aprendizado de máquina clustering para análise da causa raiz no Gerenciador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 2358cb2ea411a0077f34798183da30bd32ae067b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925119"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>O recurso no Gerenciador de dados do Azure de aprendizado de máquina

Gerenciador de dados do Azure, uma plataforma de análise de Big Data, é usado para monitorar a integridade do serviço, QoS ou dispositivos com defeito para comportamentos anormais usando interna [detecção de anomalias e a previsão](/azure/data-explorer/anomaly-detection) funções. Quando um padrão anormal é detectado, um raiz causa RCA (análise) é executada para minimizar ou resolver da anomalia.

O processo de diagnóstico é complexa e demorada e concluído por especialistas de domínio. O processo inclui a busca e a associação de dados adicionais de fontes diferentes para o mesmo intervalo de tempo, em busca de alterações na distribuição de valores em várias dimensões, variáveis adicionais, de criação de gráficos e outras técnicas com base no conhecimento do domínio e intuição. Como esses cenários de diagnóstico são comuns no Gerenciador de dados do Azure, plug-ins de aprendizado de máquina estão disponíveis para facilitar a fase de diagnóstico e reduza a duração do RCA.

O Gerenciador de dados do Azure tem três plug-ins de aprendizado de máquina: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), e [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Todos os plug-ins implementam algoritmos de clustering. O `autocluster` e `basket` plug-ins de um único conjunto de registros do cluster e o `diffpatterns` as diferenças entre dois conjuntos de registros de clusters de plug-in.

## <a name="clustering-a-single-record-set"></a>Clustering de um conjunto de registros único

Um cenário comum inclui um conjunto de dados selecionado por critérios específicos, como a janela de tempo que exibe comportamentos anormais, leituras de dispositivo de temperatura alta, os comandos de longa duração e superior, os usuários de gastos. Gostaríamos de uma maneira simples e rápida para localizar padrões comuns (segmentos) nos dados. Padrões são um subconjunto do conjunto de dados cujos registros compartilham os mesmos valores em várias dimensões (colunas categóricas). A consulta a seguir cria e mostra uma série de tempo de exceções de serviço durante uma semana em compartimentos de dez minutos:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Gráfico de tempo de exceções de serviço](media/machine-learning-clustering/service-exceptions-timechart.png)

A contagem de exceção do serviço se correlaciona com geral tráfego de serviço. Você pode ver claramente o diário padrão, por dias úteis de segunda a sexta-feira, com um aumento no serviço de exceção contagens de meio-dia e descarta nas contagens de durante a noite. Contagens de baixos simples são visíveis no final de semana. Picos de exceção podem ser detectados usando [detecção de anomalias de série de tempo](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) no Gerenciador de dados do Azure.

O segundo pico nos dados ocorre na terça-feira tarde. A consulta a seguir é usada para diagnosticar esse pico. Use a consulta para redesenhar o gráfico ao redor o pico em uma resolução mais alta (oito horas em compartimentos de um minuto) para verificar se ele é um pico agudo e exiba suas bordas.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Concentre-se no gráfico de tempo de pico](media/machine-learning-clustering/focus-spike-timechart.png)

Podemos ver um pico de dois minutos estreito de 15:00:02 de 15. Na consulta a seguir, conte as exceções nessa janela de dois minutos:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Contagem |
|---------|
|972    |

Na consulta a seguir, 20 exceções fora 972 de exemplo:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Região | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Use `autocluster()` para registro individual, definir o clustering

Mesmo que haja menos de mil exceções, é ainda difícil localizar segmentos comuns, como há vários valores em cada coluna. Você pode usar [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) plug-in para extrair uma pequena lista de segmentos comuns e localizar o interessante instantaneamente clusters em até dois minutos do pico, conforme mostrado na consulta a seguir:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Contagem | Porcentagem | Região | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Você pode ver nos resultados acima que o segmento mais dominantes nos contém 65.74% dos registros de exceção total e compartilha quatro dimensões. O próximo segmento é muito menos comum, contém apenas 9,67% dos registros e compartilha as três dimensões. Os outros segmentos são até mesmo menos comuns. 

Autocluster usa um algoritmo de proprietário para várias dimensões de mineração e extrair segmentos interessantes. "Interessante" significa que cada segmento tem cobertura significativa do que o conjunto de registros e o conjunto de recursos. Os segmentos são divergidos também, o que significa que cada um deles é significativamente diferente das outras. Um ou mais desses segmentos podem ser relevantes para o processo RCA. Para minimizar a avaliação e revisão de segmento, autocluster extrai apenas uma lista pequena de segmento.

### <a name="use-basket-for-single-record-set-clustering"></a>Use `basket()` para registro individual, definir o clustering

Você também pode usar o [ `basket()` ](/azure/kusto/query/basketplugin) plug-in, conforme mostrado na consulta a seguir:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Contagem | Porcentagem | Região | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Carrinho de compras implementa o algoritmo Apriori item de conjunto de mineração e extrai todos os segmentos cuja cobertura do conjunto de registros está acima do limite (% 5 por padrão). Você pode ver o que mais segmentos foram extraídos com rostos semelhantes (por exemplo, segmentos de 0,1 ou 2,3).

Ambos os plug-ins são poderosas e fáceis de usar, mas sua maior limitação significativa é devido ao fato de que eles um único registro definido de maneira sem supervisão (com nenhum rótulo) do cluster. Portanto, é claro se os padrões extraídos caracterizam o conjunto de registros selecionado (os registros anormais) ou o conjunto de registros global.

## <a name="clustering-the-difference-between-two-records-sets"></a>A diferença entre os conjuntos de dois registros de clustering

O [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) plug-in supera a limitação da `autocluster` e `basket`. `Diffpatterns` usa dois conjuntos de registros e extrai os segmentos principais que são diferentes entre eles. Um conjunto geralmente contém o conjunto que está sendo investigado de registros anormal (um analisados pelos `autocluster` e `basket`). O outro conjunto contém o conjunto de registros de referência (linha de base). 

Na consulta a seguir, usamos `diffpatterns` localizar clusters interessantes em até dois minutos do pico, que são diferentes de clusters de dentro da linha de base. Podemos definir a janela de linha de base como oito minutos antes de 15:00 (quando o pico iniciado). Também precisamos estender uma coluna binária (AB) especificando se um registro específico pertence à linha de base ou para o conjunto anormal. `Diffpatterns` implementa um algoritmo de aprendizado supervisionado, onde os rótulos de dois classe foram gerados por anômalos em comparação com o sinalizador de linha de base (AB).

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Região | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | EAU | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

O segmento mais dominantes nos é o mesmo segmento que foi extraído por `autocluster`, sua cobertura na janela anormal de dois minutos também é 65.74%. Mas sua cobertura na janela de linha de base de oito minutos apenas 1.7%. A diferença é % 64.04. Essa diferença parece estar relacionado ao pico anormal. Você pode verificar essa suposição, dividindo o gráfico original nos registros que pertencem a este segmento problemático em comparação com os outros segmentos, conforme mostrado na consulta a seguir:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Validando timechart de segmento 'diffpattern'](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Este gráfico nos permite ver que o pico na terça-feira tarde foi devido a exceções esse segmento específico, descobertos usando o `diffpatterns` plug-in.

## <a name="summary"></a>Resumo

Os plug-ins do Gerenciador de dados de Azure Machine Learning são úteis para muitos cenários. O `autocluster` e `basket` implementar o algoritmo de aprendizado sem supervisão e são fáceis de usar. `Diffpatterns` implementa o algoritmo de aprendizado de supervisionado e, embora mais complexas, ela é mais eficiente na extração segmentos de diferenciação para RCA.

Esses plug-ins são usados interativamente em cenários de ad hoc e em automático perto de serviços de monitoramento em tempo real. No Gerenciador de dados do Azure, detecção de anomalias de série temporal é seguida por um processo de diagnóstico que é altamente otimizado para atender aos padrões de desempenho necessário.
