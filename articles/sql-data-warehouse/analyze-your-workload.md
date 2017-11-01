---
title: "Analisar sua carga de trabalho – SQL Data Warehouse do Azure | Microsoft Docs"
description: "Técnicas para analisar a priorização de consulta para sua carga de trabalho no SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>Analisar sua carga de trabalho
Técnicas para analisar a priorização de consulta para sua carga de trabalho no SQL Data Warehouse do Azure.

## <a name="workload-groups"></a>Grupos de carga de trabalho 
O SQL Data Warehouse implementa classes de recursos usando grupos de carga de trabalho. Há um total de oito grupos de carga de trabalho que controlam o comportamento das classes de recurso entre os vários tamanhos de DWU. Para qualquer DWU, o SQL Data Warehouse usa somente quatro dos oito grupos de carga de trabalho. Isso faz sentido, pois cada grupo de carga de trabalho é atribuído a uma das quatro classes de recurso: smallrc, mediumrc, largerc ou xlargerc. A importância de entender esses grupos de carga de trabalho é que alguns deles são definidos com *importância*maior. A importância é usada para agendamento de CPU. As consultas executadas com importância alta obterão três vezes mais ciclos de CPU do que aquelas com importância média. Portanto, os mapeamentos de slot de simultaneidade também determinam a prioridade da CPU. Quando uma consulta consome 16 ou mais slots, ela é executada com alta importância.

A tabela a seguir mostra os mapeamentos de importância para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para slots de simultaneidade e importância

| Grupos de carga de trabalho | Mapeamento do slot de simultaneidade | MB/Distribuição (Elasticidade) | MB/Distribuição (Computação) | Mapeamento de importância |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Média             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Média             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Média             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Média             |
| SloDWGroupC04   | 16                       |  1.600                         | 4000                        | Alto               |
| SloDWGroupC05   | 32                       |  3.200                         | 8000                        | Alto               |
| SloDWGroupC06   | 64                       |  6.400                         | 16.000                      | Alto               |
| SloDWGroupC07   | 128                      | 12.800                         | 32.000                      | Alto               |
| SloDWGroupC08   | 256                      | 25.600                         | 64.000                      | Alto               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
Da tabela **Alocação e consumo de slots de simultaneidade** , podemos ver que um DW500 usa um, quatro, oito ou 16 slots de simultaneidade para smallrc, mediumrc, largerc e xlargerc, respectivamente. Você pode procurar esses valores na tabela anterior para localizar a importância de cada classe de recurso.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapeamento do DW500 para obter a importância das classes de recurso
| classe de recurso | Grupo de carga de trabalho | Slots de simultaneidade usados | MB / Distribuição | importância |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Média     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Média     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Média     |
| xlargerc       | SloDWGroupC04  | 16                     | 1.600             | Alto       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Média     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Média     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Média     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Média     |
| staticrc50     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc60     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc70     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc80     | SloDWGroupC03  | 16                     | 1.600             | Alto       |

## <a name="view-workload-groups"></a>Exibir grupos de carga de trabalho
Você pode usar a seguinte consulta DMV para examinar as diferenças na alocação de recurso de memória em detalhes da perspectiva do administrador de recursos ou para analisar o uso ativo e histórico dos grupos de carga de trabalho ao solucionar problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Detecção de consulta enfileirada e outros DMVs
Você pode usar o DMV `sys.dm_pdw_exec_requests` para identificar consultas que estão aguardando em uma fila de simultaneidade. As consultas que estão aguardando um slot de simultaneidade terão um status de **suspensas**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

As funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A consulta a seguir mostra a qual função cada usuário está atribuído.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

O SQL Data Warehouse tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas que ficam fora da estrutura de slot de simultaneidade. Consultas DMV e funções de sistema, como `SELECT @@VERSION` , são exemplos de consultas de locais.
* **UserConcurrencyResourceType**: consultas que ficam dentro da estrutura de slot de simultaneidade. Consultas em tabelas do usuário final representam exemplos que usariam esse tipo de recurso.
* **DmsConcurrencyResourceType**: esperas resultantes de operações de movimentação de dados.
* **BackupConcurrencyResourceType**: essa espera indica que está sendo feito backup de um banco de dados. O valor máximo para esse tipo de recurso é 1. Se vários backups foram solicitados ao mesmo tempo, os outros serão colocados em fila.

O DMV `sys.dm_pdw_waits` pode ser usado para ver quais recursos uma solicitação está aguardando.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

O DMV `sys.dm_pdw_resource_waits` mostra apenas as esperas de recursos consumidas por determinada consulta. O tempo de espera do recurso mede apenas o tempo de espera dos recursos a serem fornecidos, não o tempo de espera do sinal, que é o tempo necessário para o SQL Server subjacente agendar a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Você também pode usar o `sys.dm_pdw_resource_waits` DMV calcular quantos slots de simultaneidade foram concedidos.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O DMV `sys.dm_pdw_wait_stats` pode ser usado para análise de tendências históricas de espera.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como gerenciar usuários de banco de dados e segurança, confira [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como classes de recurso maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [Recriando índices para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Recriando índices para melhorar a qualidade de segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
