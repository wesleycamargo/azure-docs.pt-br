---
title: Solucionar problemas de receptores de Hub de Eventos no Azure Stream Analytics
description: Este artigo descreve como usar vários grupos de consumidores para entradas de Hubs de Eventos em trabalhos do Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Solucionar problemas de receptores de Hub de Eventos no Azure Stream Analytics

Você pode usar os Hubs de Eventos do Azure no Stream Analytics do Azure para ingerir ou gerar dados de um trabalho. Uma prática recomendada para usar Hubs de Eventos é usar vários grupos de consumidores a fim de garantir a escalabilidade do trabalho. Um dos motivos é que o número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores em um único grupo de consumidores. O número exato de receptores baseia-se nos detalhes da implementação interna para a lógica de topologia de expansão. O número de receptores não é exposto externamente. O número de leitores pode ser alterado na hora de início ou durante os upgrades do trabalho.

O erro mostrado quando o número de receptores excede o máximo é: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando o número de leitores muda durante o upgrade de um trabalho, avisos temporários são gravados nos logs de auditoria. Os trabalhos do Stream Analytics são recuperados automaticamente desses problemas transitórios.

## <a name="add-a-consumer-group-in-event-hubs"></a>Adicionar um grupo de consumidores nos Hubs de Eventos
Para adicionar um novo grupo de consumidores à instância dos Hubs de Eventos, execute estas etapas:

1. Entre no Portal do Azure.

2. Localize seus Hubs de Eventos.

3. Selecione **Hubs de Eventos** sob o **Entidades**.

4. Selecione o Hub de Eventos por nome.

5. Na página **Instância de Hubs de Eventos** no cabeçalho **Entidades**, selecione **Grupos de consumidores**. Um grupo de consumidores com nome **$Default** está listado.

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicionar um grupo de consumidores nos Hubs de Eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho do Stream Analytics para apontar para o Hub de Eventos, você especificou o grupo de consumidores nele. $Default é usado quando nenhum for especificado. Depois de criar um novo grupo de consumidores, edite a entrada do Hub de Eventos no trabalho do Stream Analytics e especifique o nome do novo grupo de consumidores.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Número de leitores por partição excede o limite de cinco dos Hubs de Eventos

Se a sintaxe de consulta de streaming fizer referência ao mesmo recurso do Hub de Eventos de entrada várias vezes, o mecanismo de trabalho poderá usar vários leitores por consulta desse mesmo grupo de consumidores. Quando há um número excessivo de referências para o mesmo grupo de consumidores, o trabalho pode exceder o limite de cinco e gerar um erro. Nessas circunstâncias, você pode dividir ainda mais usando várias entradas em vários grupos de consumidores utilizando a solução descrita na seção a seguir. 

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


## <a name="next-steps"></a>Próximas etapas
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
