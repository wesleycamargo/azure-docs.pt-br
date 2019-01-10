---
title: Logs do servidor para Banco de Dados do Azure para MySQL
description: Descreve os logs disponíveis no Banco de Dados do Azure para MySQL para os parâmetros disponíveis para habilitar níveis de log diferentes.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 10/03/2018
ms.openlocfilehash: c9f8fc4bee370f287b40275b76fa98d2552d7600
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545066"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Logs de servidor no Banco de Dados do Azure para MySQL
No Banco de Dados do Azure para MySQL, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas. 

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte o manual de referência de MySQL [seção de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Acessar logs do servidor
Você pode listar e baixar logs de servidor do Banco de Dados do Azure para MySQL usando o Portal do Azure a CLI do Azure.

No Portal do Azure, selecione o servidor do Banco de Dados do Azure para MySQL. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**.

Para obter mais informações sobre a CLI do Azure, consulte [Configurar e acessar logs de servidor usando a CLI do Azure](howto-configure-server-logs-in-cli.md).

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

Consulte a [documentação de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL para descrições completas dos parâmetros de log de consultas lentas.

## <a name="diagnostic-logs"></a>Logs de diagnóstico
O Banco de Dados do Azure para MySQL é integrado aos Logs de Diagnóstico do Monitor do Azure. Depois de ativar os logs de consulta lenta em seu servidor MySQL, você pode optar por emiti-los para o Log Analytics, Event Hubs ou Azure Storage. Para saber mais sobre como ativar logs de diagnóstico, consulte o como parte da [documentação registros de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md).

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|---|
| TenantId | Sua ID de locatário |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| Tipo | Tipo do log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID para a assinatura a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos ao qual o servidor pertence |
| ResourceProvider | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recurso |
| Recurso | Nome do servidor |
| Categoria | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | Nome do servidor |
| start_time_t [UTC] | Horário em que a consulta começou |
| query_time_s | Tempo total que a consulta levou para executar |
| lock_time_s | Tempo total em que a consulta foi bloqueada |
| user_host_s | Nome de Usuário |
| rows_sent_s | Número de linhas enviadas |
| rows_examined_s | Número de linhas verificadas |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | Inserir id |
| sql_text_s | Consulta completa |
| server_id_s | Id do servidor |
| thread_id_s | id do thread |
| \_ResourceId | URI de recurso |

## <a name="next-steps"></a>Próximas etapas
- [Como configurar e acessar logs de servidor por meio da CLI do Azure](howto-configure-server-logs-in-cli.md).
