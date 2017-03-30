---
title: "Noções básicas sobre monitoramento de trabalho do Stream Analytics | Microsoft Docs"
description: "Noções básicas sobre monitoramento de trabalho do Stream Analytics"
keywords: monitor de consultas
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: e9ea21be1fabb5846c4f3b7b05f3a93298830e57
ms.openlocfilehash: 5119d1d6c02b8e080a9e0905ae21a2bddefab03a
ms.lasthandoff: 01/05/2017


---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Noções básicas sobre o monitoramento de trabalhos do Stream Analytics e como monitorar consultas

## <a name="introduction-the-monitor-page"></a>Introdução: Página do monitor
O Portal do Azure exibe as principais métricas de desempenho que podem ser usadas para monitorar e solucionar problemas de desempenho de seus trabalhos e consultas. Para ver essas métricas, procure o trabalho do Stream Analytics em cujas métricas você está interessado em ver e exiba a seção **Monitoramento** na página de visão geral.  

![Link de monitoramento](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

A janela será exibida conforme mostrado:

![Painel de Trabalho de monitoramento](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para o Stream Analytics
| Métrica                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| % de utilização do SU       | A utilização das unidades de Streaming é atribuída a um trabalho na guia Escala do trabalho. Se esse indicador alcançar 80% ou acima, há grande probabilidade de que o processamento de eventos se atrase ou pare. |
| Eventos de entrada           | Quantidade de dados recebidos pelo trabalho do Stream Analytics, em termos de eventos. Isso pode ser usado para validar que os eventos estão sendo enviados para a fonte de entrada. |
| Eventos de saída          | Quantidade de dados enviados pelo trabalho do Stream Analytics para o destino de saída, em números de evento. |
| Eventos fora de ordem    | Número de eventos recebidos fora de ordem que foram descartados ou que receberam um carimbo de data/hora ajustado, com base na Política de ordenação de evento. Isso pode ser afetado pela configuração da definição da Janela de tolerância fora de ordem. |
| Erros de conversão de dados | Número de erros de conversão de dados gerado por um trabalho do Stream Analytics. |
| Erros de tempo de execução         | O número total de erros que ocorrem durante a execução de um trabalho do Stream Analytics. |
| Eventos de entrada atrasados      | Número de eventos que chegam atrasados da fonte que podem ter sido descartados ou ter tido o carimbo de data/hora ajustado, com base na configuração de Política de ordenação de eventos da configuração da definição da Janela de tolerância de chegada atrasada. |
| Solicitações de função      | Número de chamadas à função Azure Machine Learning (se presente). |
| Solicitações de função com falha | Número de chamadas à função Azure Machine Learning com falha (se presente). |
| Eventos de função        | Número de eventos enviados à função Azure Machine Learning (se presente). |
| Bytes de evento de entrada      | Quantidade de dados recebidos pelo trabalho do Stream Analytics, em bytes. Isso pode ser usado para validar que os eventos estão sendo enviados para a fonte de entrada. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizando o Monitoramento no Portal do Azure
Você pode ajustar o tipo de gráfico, as métricas mostradas e o intervalo de hora nas configurações de Editar Gráfico. Para obter detalhes, veja [Como personalizar o monitoramento](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Gráfico do Tempo do Monitor de Consultas](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


