---
title: Logs de servidor para Banco de Dados do Azure para MySQL | Microsoft Docs
description: "Descreve os logs disponíveis no Banco de Dados do Azure para MySQL para os parâmetros disponíveis para habilitar níveis de log diferentes."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 13b30df82c1a6c4c45a621a1f7a40148a55a7648
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2017
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Logs de servidor no Banco de Dados do Azure para MySQL
No Banco de Dados do Azure para MySQL, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar afunilamentos de desempenho para solução de problemas. 

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte o manual de referência de MySQL [seção de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Acessar logs do servidor
Você pode listar e baixar logs de servidor do Banco de Dados do Azure para MySQL usando o Portal do Azure a CLI do Azure.

No Portal do Azure, selecione o servidor do Banco de Dados do Azure para MySQL. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**.

Para obter mais informações sobre a CLI do Azure, consulte [Configurar e acessar logs de servidor usando a CLI do Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Retenção de log
Logs estão disponíveis por até sete dias desde a criação deles. Se o tamanho total dos logs disponíveis exceder 7,5 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível. 

Logs são reciclados a cada 24 horas ou 7,5 GB, o que ocorrer primeiro.


## <a name="configure-logging"></a>Configurar o registro em log 
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log para ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: este parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.

Consulte a [documentação de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL para descrições completas dos parâmetros de log de consultas lentas.

## <a name="next-steps"></a>Próximas etapas
- [Como configurar e acessar logs de servidor por meio da CLI do Azure](howto-configure-server-logs-in-cli.md).
