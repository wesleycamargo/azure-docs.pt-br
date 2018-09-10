---
title: Capacidade de gerenciamento e monitoramento do SQL Data Warehouse do Azure – atividade de consulta e utilização de recursos | Microsoft Docs
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar o SQL Data Warehouse do Azure. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c783045d242725ee19dfe7e0baee13625d986312
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246487"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorando a atividade de consulta e a utilização de recursos no SQL Data Warehouse do Azure
O SQL Data Warehouse do Azure oferece uma rica experiência de monitoramento no portal do Azure para gerar insights para sua carga de trabalho do data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite a integração com outros serviços de monitoramento do Azure, como o OMS (Operations Management Suite)/ Log Analytics e Azure Monitor para fornecer uma experiência de monitoramento holística não apenas para seu data warehouse, mas também para toda a plataforma de análise do Azure para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilização de recursos 
As métricas a seguir estão disponíveis no portal do Azure.

| Nome da métrica                           | DESCRIÇÃO     | Tipo de agregação |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Percentual de CPU                          | Utilização da CPU em todos os nós para o data warehouse | Máximo      |
| Porcentagem de E/S de dados                      | Utilização de E/S em todos os nós para o data warehouse | Máximo   |
| Conexões bem sucedidas                  | Número de conexões bem-sucedidas com os dados | Total            |
| Conexões com falha                      | Número de conexões com falha com o data warehouse | Total            |
| Bloqueado pelo firewall                     | Número de logons para o data warehouse que foram bloqueados | Total            |
| Limite de DWU                              | Objetivo de nível de serviço do data warehouse | Máximo   |
| Porcentagem de DWU                          | Máximo entre o percentual de CPU e o percentual de E/S de dados | Máximo   |
| DWU usado                                | Limite de DWU * percentual de DWU | Máximo   |
| Percentual de ocorrência no cache | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Máximo |
| Percentual de cache usado | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Máximo |

## <a name="query-activity"></a>Consultar atividade
Para uma experiência de programação ao monitorar o SQL Data Warehouse por meio do T-SQL, o serviço fornece um conjunto de DMVs (Exibições de Gerenciamento Dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL Data Warehouse fornece, consulte esta [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas
As métricas e os logs podem ser exportados para [OMS (Operations Management Suite)](https://azure.microsoft.com/resources/videos/operations-management-suite-oms-overview/), especificamente o componente [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), e podem ser acessados programaticamente por meio da [Pesquisa de Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata).


> [!NOTE]
> A partir de agosto de 2018, os logs estão sendo implementados para o SQL Data Warehouse

## <a name="next-steps"></a>Próximas etapas
Os guias de instruções a seguir descrevem cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

