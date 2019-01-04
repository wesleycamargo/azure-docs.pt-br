---
title: Logs de servidor no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como o Banco de Dados do Azure para PostgreSQL gera logs de erros e consultas e como a retenção de log é configurada.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 0e2dc2af6b4c7ddf531458136e6bcabb49be3b8f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538798"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Logs de servidor no Banco de Dados do Azure para PostgreSQL 
Banco de Dados do Azure para PostgreSQL gera logs de consulta e de erro. Os logs de erro e consulta podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho abaixo do ideal. (O acesso aos logs de transação não está incluído). 

## <a name="configure-logging"></a>Configurar o registro em log 
Você pode configurar o registro em log no seu servidor usando os parâmetros de registro em log. Em cada novo servidor **log_checkpoints** e **log_connections** são ativados por padrão. Há parâmetros adicionais que você pode ajustar para atender às suas necessidades de registro em log: 

![Banco de Dados do Azure para PostgreSQL – parâmetros de registro em log](./media/concepts-server-logs/log-parameters.png)

Para obter mais informações sobre esses parâmetros, consulte a documentação [Relatório e registro em log de erros](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) do PostgreSQL. Para saber como configurar parâmetros do Banco de Dados do Azure para PostgreSQL, consulte a [documentação do portal](howto-configure-server-parameters-using-portal.md) ou a [documentação da CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Acessar logs do servidor por meio do portal ou da CLI
Se você habilitou os logs, poderá acessá-los do armazenamento de logs do Banco de Dados do Azure para PostgreSQL usando o [portal do Azure](howto-configure-server-logs-in-portal.md), a [CLI do Azure](howto-configure-server-logs-using-cli.md) e as APIs REST do Azure. Os arquivos de log fazem um rodízio a cada uma hora ou a cada 100 MB, o que ocorrer primeiro. Você pode definir o período de retenção desse armazenamento de logs usando o parâmetro  **log\_retention\_period**  associado ao seu servidor. O valor padrão é de 3 dias; o valor máximo é de 7 dias. O servidor deve ter armazenamento alocado suficiente para armazenar os arquivos de log. (Esse parâmetro de retenção não controla os Logs de Diagnóstico do Azure).


## <a name="diagnostic-logs"></a>Logs de diagnóstico
O Banco de Dados do Azure para PostgreSQL é integrado aos Logs de Diagnóstico do Azure Monitor. Depois de habilitar os logs em seu servidor PostgreSQL, você pode optar por emiti-los no [Log Analytics](../azure-monitor/log-query/log-query-overview.md), nos Hubs de Eventos ou no Armazenamento do Azure. Para saber mais sobre como habilitar logs de diagnóstico, consulte a seção de instruções da [documentação logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md). 


A tabela a seguir descreve o que está em cada log. Dependendo do ponto de extremidade de saída escolhido, os campos incluídos e a ordem em que aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua ID de locatário |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| Tipo | Tipo do log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID para a assinatura a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos ao qual o servidor pertence |
| ResourceProvider | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recurso |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Nível de log, exemplo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem de log primária | 
| Domínio | Versão do servidor, o exemplo: postgres-10 |
| Detalhes | Mensagem de log secundária (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| LogicalServerName | Nome do servidor | 
| _ResourceId | URI de recurso |

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como acessar logs no [portal do Azure](howto-configure-server-logs-in-portal.md) ou na [CLI do Azure](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre o [preço do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
