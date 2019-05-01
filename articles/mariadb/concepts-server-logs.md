---
title: Logs do servidor para Banco de Dados do Azure para MariaDB
description: Descreve os logs disponíveis no Banco de Dados do Azure para MariaDB para os parâmetros disponíveis para habilitar níveis de log diferentes.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 8a78a9b8f0772a83e45ac2b926878e61e6ee2e61
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926326"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Logs de servidor no Banco de Dados do Azure para MariaDB
No Banco de Dados do Azure para MariaDB, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consulta lenta, leia a documentação do MariaDB para [log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Acessar logs do servidor
É possível listar e baixar logs de servidor do Banco de Dados do Azure para MariaDB usando o portal do Azure e a CLI do Azure.

No portal do Azure, selecione o servidor do Banco de Dados do Azure para MariaDB. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**.

Para obter mais informações sobre a CLI do Azure, consulte [Configurar e acessar logs de servidor usando a CLI do Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Retenção de log
Logs estão disponíveis por até sete dias desde a criação deles. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Logs são reciclados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-logging"></a>Configurar o registro em log
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log para ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: Esse parâmetro limita o número de consultas não indexadas que podem ser gravadas no log de consulta lenta. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.

Consulte a [documentação de log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/) do MariaDB para ver descrições completas dos parâmetros de log de consulta lenta.

## <a name="diagnostic-logs"></a>Logs de diagnóstico
Banco de dados do Azure para MariaDB é integrado com os Logs de diagnóstico do Azure Monitor. Depois de habilitar logs de consulta lentos em seu servidor do MariaDB, você pode optar por fazer com que eles são emitidos para o armazenamento do Azure, Hubs de eventos ou logs do Azure Monitor. Para saber mais sobre como ativar logs de diagnóstico, consulte o como parte da [documentação registros de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs do servidor só está disponível no uso geral e otimizado para memória [tipos de preço](concepts-pricing-tiers.md).

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` [UTC] | Horário em que a consulta começou |
| `query_time_s` | Tempo total que a consulta levou para executar |
| `lock_time_s` | Tempo total em que a consulta foi bloqueada |
| `user_host_s` | Nome de Usuário |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas verificadas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

## <a name="next-steps"></a>Próximas etapas
- [Como configurar e acessar logs de servidor a partir da CLI do Azure](howto-configure-server-logs-portal.md).
