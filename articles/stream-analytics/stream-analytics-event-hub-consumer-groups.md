---
title: Solucionar problemas de receptores de hub de eventos no Azure Stream Analytics
description: Práticas recomendadas de consulta para consideração de grupos de consumidores dos Hubs de Eventos em trabalhos do Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 20614986fc6c6afa9a92d163bf973a148e0517c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Depurar o Stream Analytics do Azure com receptores do hub de eventos

Você pode usar os Hubs de Eventos do Azure no Stream Analytics do Azure para ingerir ou gerar dados de um trabalho. Uma prática recomendada para usar Hubs de Eventos é usar vários grupos de consumidores a fim de garantir a escalabilidade do trabalho. Um dos motivos é que o número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores em um único grupo de consumidores. O número exato de receptores baseia-se nos detalhes da implementação interna para a lógica de topologia de expansão. O número de receptores não é exposto externamente. O número de leitores pode ser alterado na hora de início ou durante os upgrades do trabalho.

> [!NOTE]
> Quando o número de leitores muda durante o upgrade de um trabalho, avisos temporários são gravados nos logs de auditoria. Os trabalhos do Stream Analytics são recuperados automaticamente desses problemas transitórios.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Número de leitores por partição excede o limite de cinco dos Hubs de Eventos

Os cenários em que o número de leitores por partição excede o limite de cinco dos Hubs de Eventos incluem os seguintes:

* Várias instruções SELECT: se você usar várias instruções SELECT que se refiram à **mesma** entrada do hub de eventos, cada instrução SELECT fará com que um novo receptor seja criado.
* UNION: quando você usa UNION, é possível ter várias entradas que se refiram ao **mesmo** grupo de consumidores e hub de eventos.
* SELF JOIN: quando você usa uma operação SELF JOIN, é possível se referir ao **mesmo** hub de eventos várias vezes.

## <a name="solution"></a>Solução

As práticas recomendadas a seguir podem ajudar a minimizar cenários nos quais o número de leitores por partição excede o limite de cinco de Hubs de Eventos.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dividir a consulta em várias etapas usando uma cláusula WITH

A cláusula WITH especifica um conjunto de resultados nomeado temporário que pode ser referenciado por uma cláusula FROM na consulta. Você define a cláusula WITH no escopo de execução de uma única instrução SELECT.

Por exemplo, em vez desta consulta:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Use esta consulta:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Garantir que as entradas se associem a diferentes grupos de consumidores

Para consultas em que três ou mais entradas estão conectadas ao mesmo grupo de consumidores de Hubs de Eventos, crie grupos de consumidores separados. Isso exige a criação de entradas adicionais do Stream Analytics.


## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, teste nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
