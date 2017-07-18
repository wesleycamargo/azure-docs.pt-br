---
title: "Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo descreve como configurar os parâmetros de servidor disponíveis no Banco de Dados do Azure para MySQL usando o portal do Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure e lista os parâmetros com suporte, os valores padrão e o intervalo de valores válidos. Nem todos os parâmetros de servidor podem ser ajustados. Há suporte apenas para aqueles listados aqui.

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Navegue para a folha Parâmetros de Servidor no portal do Azure

Faça logon no portal do Azure e, depois, clique no nome do Banco de Dados do Azure para MySQL Server. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros de servidor** para abrir a folha Parâmetros de servidor do Banco de Dados do Azure para MySQL.

![Folha parâmetros de servidor no portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A tabela a seguir lista os parâmetros de servidor com suporte no momento. Os parâmetros podem ser configurados de acordo com os requisitos do aplicativo.

> [!div class="mx-tableFixed"]
|Nome do Parâmetro|Valor Padrão|Intervalo|Descrição|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|Controla quantos microssegundos a confirmação de log binário aguarda antes de sincronizar o arquivo de log binário em disco.|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|O número máximo de transações a serem aguardadas antes da anulação do atraso atual, conforme especificado por binlog-group-commit-sync-delay.|
|character_set_server|LATIN1|BIG5, UTF8MB4, etc.|Use charset_name como o conjunto de caracteres do servidor padrão.|
|div_precision_increment|4|0-30|Número de dígitos pelos quais aumentar a escala do resultado das operações de divisão.|
|group_concat_max_len|1.024|4-16777216|Tamanho máximo permitido do resultado em bytes para o GROUP_CONCAT().|
|innodb_adaptive_hash_index|ATIVADO|ATIVADO, DESATIVADO|Indica se os índices de hash adaptáveis do innodb são habilitados ou desabilitados.|
|innodb_lock_wait_timeout|50|1-3600|A duração em segundos que uma transação do InnoDB aguarda um bloqueio de linha antes de abandonar o processo.|
|interactive_timeout|1800|10-1800|Número de segundos durante os quais o servidor aguarda uma atividade em uma conexão interativa antes de fechá-la.|
|log_bin_trust_function_creators|DESATIVADO|ATIVADO, DESATIVADO|Essa variável se aplica quando o log binário está habilitado. Ela controla se os criadores da função armazenada podem ser confiáveis em não criar funções armazenadas que fazem com que eventos não seguros sejam gravados no log binário.|
|log_queries_not_using_indexes|DESATIVADO|ATIVADO, DESATIVADO|Registra as consultas que devem recuperar todas as linhas para o log de consultas lentas.|
|log_slow_admin_statements|DESATIVADO|ATIVADO, DESATIVADO|Inclui instruções administrativas lentas nas instruções gravadas no log de consultas lentas.|
|log_throttle_queries_not_using_indexes|0|0-4294967295|Limita o número de consultas desse tipo por minuto que podem ser gravadas no log de consultas lentas.|
|long_query_time|10|1-1E+100|Se uma consulta levar mais tempo do que essa quantidade de segundos, o servidor incrementará a variável de status Slow_queries.|
|max_allowed_packet|536870912|1024-1073741824|O tamanho máximo de um pacote ou de qualquer cadeia de caracteres gerada/intermediária ou qualquer parâmetro enviado pela função de API do C mysql_stmt_send_long_data().|
|min_examined_row_limit|0|0-18446744073709551615|Registra as consultas que têm um número maior de linhas do que o número configurado de linhas no log de consultas lentas. |
|server_id|3293747068|1000-4294967295|A ID do servidor, usada na replicação para fornecer a cada mestre e subordinado uma identidade exclusiva.|
|slave_net_timeout|60|30-3600|O número de segundos durante os quais aguardar mais dados do mestre antes que o subordinado considere a conexão interrompida, anule a leitura e tente se reconectar.|
|slow_query_log|DESATIVADO|ATIVADO, DESATIVADO|Habilita ou desabilita o log de consultas lentas.|
|sql_mode|0 selecionado|ALLOW_INVALID_DATES, IGNORE_SPACE, etc.|O modo SQL do servidor atual.|
|time_zone|SYSTEM|exemplos: -8:00, +05:30|O fuso horário do servidor.|
|wait_timeout|120|60-240|O número de segundos durante os quais o servidor aguarda uma atividade em uma conexão não interativa antes de fechá-la.|

## <a name="next-steps"></a>Próximas etapas
- [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

