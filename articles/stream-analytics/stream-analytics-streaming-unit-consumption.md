---
title: 'Stream Analytics do Azure: Otimizar seu trabalho para usar eficazmente Unidades de Streaming | Microsoft Docs'
description: "Práticas recomendadas de consulta para dimensionamento e desempenho no Stream Analytics do Azure."
keywords: unidade de streaming, desempenho de consulta
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 43dcbd474bc66b194959616611aa109f1cdec230
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>Otimizar seu trabalho para usar eficazmente as Unidades de Streaming

O Stream Analytics do Azure agrega o "peso" do desempenho de executar um trabalho em SUs (Unidades de Streaming). As SUs representam os recursos de computação que são consumidos para execução de um trabalho. As SUs fornecem uma maneira de descrever a capacidade de processamento de evento relativa, com base em uma medida combinada de CPU, memória e taxas de leitura e gravação. Essa capacidade permite a você focar na lógica da consulta e elimina a necessidade de conhecer as considerações do desempenho da camada do armazenamento, de alocar manualmente memória para o trabalho e de aproximar a contagem de núcleo de CPU necessária para executar seu trabalho pontualmente.

## <a name="how-many-sus-are-required-for-a-job"></a>Quantas SUs são necessárias para um trabalho?

A escolha do número de SUs necessárias para um trabalho específico depende da configuração da partição para as entradas e da consulta que é definida dentro do trabalho. A folha **Escala** permite que você defina o número correto de SUs. Trata-se de uma prática recomendada para alocar mais SUs do que o necessário. O mecanismo de processamento do Stream Analytics foi otimizado para latência e taxa de transferência ao custo da alocação de memória adicional.

Em geral, a prática recomendada é iniciar com 6 SUs para consultas que não usam *PARTITION BY*. Em seguida, determine o ponto ideal usando um método de tentativa e erro no qual você modifica o número de SUs depois de passar volumes representativos de dados e examine a métrica %Utilization de SU.

O Stream Analytics do Azure mantém os eventos em uma janela denominada "reordenar buffer" antes de iniciar qualquer processamento. Os eventos são classificados na janela de reordenação por hora e as operações subsequentes são executadas nos eventos temporariamente classificados. A reordenação de eventos por hora garante que o operador tenha visibilidade de todos os eventos no período estipulado. Ela também permite que o operador execute o processamento de requisito e gere uma saída. Um efeito colateral desse mecanismo é que o processamento é atrasado na duração da janela para reordenar. O volume de memória do trabalho (que afeta o consumo da SU) é uma função do tamanho dessa janela de reordenação e o número de eventos contidos nela.

> [!NOTE]
> Quando o número de leitores muda durante os upgrades de trabalho, avisos temporários são gravados nos logs de auditoria. Os trabalhos do Stream Analytics são recuperados automaticamente desses problemas transitórios.

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>Causas comuns de memória alta para alto uso de SU na execução de trabalhos

### <a name="high-cardinality-for-group-by"></a>Alta cardinalidade para GROUP BY

A cardinalidade dos eventos de entrada determina o uso da memória para o trabalho.

Por exemplo, em `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)`, o número associado ao **cluster** é a cardinalidade da consulta.

Para atenuar os problemas causados pela alta cardinalidade, escale horizontalmente a consulta aumentando partições usando **PARTITION BY**.

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

O número de *cluster* é a cardinalidade de GROUP BY aqui.

Depois que a consulta é particionada, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, o que por sua vez, reduz o tamanho do buffer de reordenação. Você também deve dividir partições do hub de eventos por id de partição.

### <a name="high-unmatched-event-count-for-join"></a>Alta contagem de eventos sem correspondência para JOIN

O número de eventos sem correspondência em um JOIN afeta a utilização de memória da consulta. Por exemplo, veja uma consulta que está procurando encontrar o número de impressões de anúncio que gera cliques:

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

Nesse cenário, é possível que vários anúncios sejam mostrados e poucos cliques sejam gerados. Esse resultado exigiria que o trabalho mantivesse todos os eventos na janela de tempo. A quantidade de memória consumida é proporcional ao tamanho da janela e à taxa de eventos. 

Para atenuar essa situação, escale horizontalmente a consulta aumentando as partições usando PARTITION BY. 

Depois que a consulta é particionada, ela é espalhada em vários nós de processamento. Como resultado, o número de eventos em cada nó é reduzido, o que por sua vez, reduz o tamanho do buffer de reordenação.

### <a name="large-number-of-out-of-order-events"></a>Grande número de eventos fora de ordem 

Um grande número de eventos fora de ordem em uma grande janela de tempo fará com que o "buffer de reordenação" seja maior. Para atenuar essa situação, escale a consulta aumentando as partições usando PARTITION BY. Depois que a consulta é particionada, ela é espalhada em vários nós. Como resultado, o número de eventos em cada nó é reduzido, o que por sua vez, reduz o tamanho do buffer de reordenação. 


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

