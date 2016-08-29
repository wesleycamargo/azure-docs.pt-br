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
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Monitore sua carga de trabalho usando DMVs

Este artigo descreve como usar Visualizações de Gerenciamento Dinâmico (DMVs) para monitorar sua carga de trabalho e investigar a execução da consulta no SQL Data Warehouse do Azure.

## Conexões do monitor

A exibição [sys.dm\_pdw\_exec\_sessions][] permite monitorar as conexões ao banco de dados do Azure SQL Data Warehouse. Essa exibição contém sessões ativas, bem como um histórico das sessões desconectadas recentemente. A session\_id é a chave primária para esse modo de exibição e é atribuída em sequência para cada novo logon.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Monitorar a execução de consultas

Para monitorar a execução da consulta, comece com [sys.dm\_pdw\_exec\_requests][]. Essa exibição contém consultas em andamento, bem como um histórico das consultas concluídas recentemente. A request\_id identifica cada consulta exclusivamente e é a chave primária para essa exibição. A request\_id é atribuída em sequência para cada nova consulta. Consultar esta tabela para uma determinada session\_id mostrará todas as consultas para um logon específico.

>[AZURE.NOTE] Os procedimentos armazenados usam vários request\_ids. As ids de solicitação serão atribuídas na ordem sequencial.

Estas são as etapas para investigar os planos de execução da consulta e as horas para uma consulta específica.

### ETAPA 1: Identificar a consulta que você deseja investigar

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;
```

Nos resultados da consulta acima, **observe a ID da Solicitação** da consulta que você deseja investigar.

Consultas no estado Suspenso estão sendo enfileiradas devido a limites de simultaneidade, que são explicados detalhadamente em [Gerenciamento de simultaneidade e carga de trabalho][]. Essas consultas também aparecerão na consulta de esperas sys.dm\_pdw\_waits com um tipo de UserConcurrencyResourceType. Consultas também podem esperar por outros motivos, como bloqueios. Se sua consulta estiver aguardando um recurso, consulte [Investigar consultas aguardando recursos][].

### ETAPA 2: localizar a etapa de execução mais longa do plano de consulta

Use a ID da Solicitação para recuperar uma lista das etapas do plano de consulta de [sys.dm\_pdw\_request\_steps][]. Localize a etapa de execução longa observando o tempo total decorrido.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Verifique a coluna *operation\_type* da etapa de consulta de execução longa e observe o **Índice da Etapa**:

- Continue com a Etapa 3a para **Operações SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continue com a Etapa 3b para **Operações de movimentação de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### ETAPA 3a: localizar o progresso da execução de uma etapa SQL

Use a ID da Solicitação e o Índice de Etapas para recuperar os detalhes de [sys.dm\_pdw\_sql\_requests][], que contém informações sobre a execução da consulta em cada instância distribuída do SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Se a consulta estiver em execução, [DBCC PDW\_SHOWEXECUTIONPLAN][] poderá ser usado para recuperar o plano estimado do SQL Server do cache do plano do SQL Server para a Etapa de SQL em execução no momento em uma distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### ETAPA 3b: localizar o progresso da execução de uma etapa DMS

Use a ID da Solicitação e o Índice da Etapa para recuperar as informações sobre a Etapa de Movimentação dos Dados em execução em cada distribuição em [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Verifique a coluna *total\_elapsed\_time* para ver se uma distribuição específica está demorando muito mais do que outras para movimentar dados.
- Para a distribuição de longa execução, verifique a coluna *rows\_processed* para verificar se o número de linhas sendo movidas dessa distribuição é significativamente maior do que outros. Se for o caso, isso poderá indicar distorção de dados subjacentes.

Se a consulta estiver em execução, [DBCC PDW\_SHOWEXECUTIONPLAN][] poderá ser usado para recuperar o plano estimado do SQL Server do cache do plano do SQL Server para a Etapa de SQL em execução no momento em uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Monitorar as consultas em espera

Caso você descubra que sua consulta não está fazendo progresso porque está aguardando um recurso, veja a seguir uma consulta que mostra todos os recursos que uma consulta está aguardando.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta estiver ativamente aguardando recursos de outra consulta, o estado será **AcquireResources**. Se a consulta tiver todos os recursos necessários, o estado será **Concedido**.

## Próximas etapas
Para obter mais informações sobre as DMVs (Exibição de Gerenciamento Dinâmico), consulte [Exibições do sistema][].  
Para obter dicas sobre como gerenciar o SQL Data Warehouse, consulte [Visão geral do gerenciamento][].  
Para ver as práticas recomendadas, consulte [Práticas recomendadas do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Visão geral do gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Práticas recomendadas do SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Exibições do sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Gerenciamento de simultaneidade e carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Investigar consultas aguardando recursos]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0810_2016-->
