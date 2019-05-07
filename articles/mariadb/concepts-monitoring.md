---
title: Monitorando no Banco de Dados do Azure para MariaDB
description: Este artigo descreve as métricas de monitoramento e alerta do Banco de Dados do Azure para MariaDB, incluindo estatísticas de CPU, armazenamento e conexão.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: babe2ac55953940370daa0731463ed6ed8988502
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "64925905"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorando no Banco de Dados do Azure para MariaDB
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para MariaDB fornece várias métricas que fornecem informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-metric.md) (Como configurar alertas).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MariaDB:

|Métrica|Nome de exibição da métrica|Unidade|DESCRIÇÃO|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|active_connections|Conexões ativas|Count|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Count|O número de conexões com falha com o servidor.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|

## <a name="server-logs"></a>Logs do servidor
É possível habilitar o registro de consulta no servidor. Esses logs também estão disponíveis por meio dos Logs de diagnóstico do Azure na conta de armazenamento, Hubs de eventos e logs do Azure Monitor. Para saber mais sobre o registro, visite a página  [logs do servidor](concepts-server-logs.md).

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Consulte [Como configurar alertas](howto-alert-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
