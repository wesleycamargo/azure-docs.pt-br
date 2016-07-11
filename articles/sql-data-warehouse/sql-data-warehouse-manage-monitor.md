<properties
   pageTitle="Monitore sua carga de trabalho usando DMVs | Microsoft Azure"
   description="Aprenda a monitorar sua carga de trabalho usando DMVs."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/27/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Monitore sua carga de trabalho usando DMVs

Este artigo descreve como usar Visualizações de Gerenciamento Dinâmico (DMVs) para monitorar sua carga de trabalho e investigar a execução da consulta no SQL Data Warehouse do Azure.

## Conexões do monitor

A exibição [sys.dm\_pdw\_exec\_sessions][] permite monitorar as conexões ao banco de dados do Azure SQL Data Warehouse. Essa exibição contém sessões ativas, bem como um histórico das sessões desconectadas recentemente. A session\_id é a chave primária para esse modo de exibição e é atribuída em sequência para cada novo logon.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Investigar a execução da consulta
Para monitorar a execução da consulta, comece com [sys.dm\_pdw\_exec\_requests][]. Essa exibição contém consultas em andamento, bem como um histórico das consultas concluídas recentemente. A request\_id identifica cada consulta exclusivamente e é a chave primária para essa exibição. A request\_id é atribuída em sequência para cada nova consulta. Consultar esta tabela para uma determinada session\_id mostrará todas as consultas para um logon específico.

Estas são as etapas para investigar os planos de execução de consulta e as horas para uma consulta específica.

### ETAPA 1: Localizar a consulta a ser investigada

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find 10 queries which ran the longest
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

Observe a ID da Solicitação da consulta que você deseja investigar.

### ETAPA 2: Verificar se a consulta está aguardando recursos

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Os resultados da consulta anterior mostrarão o estado de espera da sua solicitação.

- Se a consulta está aguardando em recursos de outra consulta, o estado será **AcquireResources**.
- Se a consulta tem todos os recursos necessários e não está esperando, o estado será **Concedido**. Nesse caso, vá para observar as etapas de consulta.

### ETAPA 3: localizar a etapa de execução mais longa do plano de consulta

Use a ID da Solicitação para recuperar uma lista das etapas do plano de consulta de [sys.dm\_pdw\_request\_steps][]. Localize a etapa de execução longa observando o tempo total decorrido.

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

Salve o índice da etapa da etapa de execução longa.

Verifique a coluna *operation\_type* da etapa de consulta de execução longa:

- Continue com a etapa 4a para **operações SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continue com a etapa 4b para **Operações de movimentação de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### ETAPA 4a: localizar o progresso da execução de uma etapa SQL

Use a ID da Solicitação e o Índice de Etapa para recuperar informações de [sys.dm\_pdw\_sql\_requests][] que contenham detalhes sobre a execução da consulta em instâncias distribuídas do SQL Server. Observe a ID de Distribuição e o SPID se a consulta ainda estiver em execução e você desejar obter o plano da distribuição do SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


Se a consulta estiver em execução no momento, [DBCC PDW\_SHOWEXECUTIONPLAN][] poderá ser usado para recuperar o plano de execução do SQL Server para a Etapa de SQL em execução no momento para uma distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### ETAPA 4b: localizar o progresso da execução de uma etapa DMS

Use a ID da Solicitação e o Índice de Etapa para recuperar informações sobre a etapa de movimentação de dados em execução em cada distribuição de [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Verifique a coluna *total\_elapsed\_time* para ver se uma distribuição específica está demorando muito mais do que outras para movimentar dados.
- Para a distribuição de longa execução, verifique a coluna *rows\_processed* para verificar se o número de linhas sendo movidas dessa distribuição é significativamente maior do que outros. Se for o caso, isso poderá indicar distorção de dados subjacentes.

Se a consulta estiver em execução no momento, [DBCC PDW\_SHOWEXECUTIONPLAN][] poderá ser usado para recuperar o plano de execução do SQL Server para a Etapa de DMS em execução no momento para uma distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);

```

## Próximas etapas
Para obter mais informações sobre DMVs (exibições de gerenciamento dinâmico), consulte [Exibições do sistema][]. Para obter dicas sobre como gerenciar o SQL Data Warehouse, consulte [visão geral de gerenciamento][]. Para práticas recomendadas, consulte [Práticas recomendadas do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[manage data skew for distributed tables]: sql-data-warehouse-manage-distributed-data-skew.md
[visão geral de gerenciamento]: sql-data-warehouse-overview-manage.md
[Práticas recomendadas do SQL Data Warehouse]: sql-data-warehouse-best-practices.md
[Exibições do sistema]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0629_2016-->