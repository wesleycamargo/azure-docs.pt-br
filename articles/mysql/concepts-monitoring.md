---
title: Monitoramento no Banco de Dados do Azure para MySQL
description: Este artigo descreve as métricas de monitoramento e alertas para o Banco de Dados do Azure para MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: af90fb4c89cf552d4c5637db08ef0acd9984b31b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264690"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoramento no Banco de Dados do Azure para MySQL
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para MySQL oferece várias métricas que fornecem informações sobre o comportamento dos recursos compatíveis com o servidor MySQL. 

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-on-metric.md) (Como configurar alertas). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MySQL:

|Métrica|Nome de exibição da métrica|Unidade|DESCRIÇÃO|
|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço inclui os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|active_connections|Total de conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Total de conexões com falha|Contagem|O número de conexões com falha com o servidor.|


## <a name="next-steps"></a>Próximas etapas
- Consulte [Como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
