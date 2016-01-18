<properties
   pageTitle="Monitore sua carga de trabalho usando DMVs | Microsoft Azure"
   description="Aprenda a monitorar sua carga de trabalho usando DMVs."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="sahajs"/>

# Monitore sua carga de trabalho usando DMVs

Este artigo descreve como usar Visualizações de Gerenciamento Dinâmico (DMVs) para monitorar sua carga de trabalho e investigar a execução da consulta no SQL Data Warehouse do Azure.



## Conexões do monitor

É possível usar a visualização *sys.dm\_pdw\_nodes\_exec\_connections* para recuperar informações sobre as conexões estabelecidas com seu banco de dados do SQL Data Warehouse do Azure. Além disso, a exibição *sys.dm\_exec\_sessions* é útil ao recuperar informações sobre todas as conexões de usuário ativas.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Use a seguinte consulta para recuperar as informações sobre a conexão atual.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## Investigar a execução da consulta
É possível encontrar situações em que a consulta não está concluindo ou está em execução mais tempo do que esperado. Nesses casos, você pode usar as etapas a seguir para coletar dados e reduzir o problema.



### ETAPA 1: Localizar a consulta a ser investigada

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Salve a ID da solicitação da consulta.


  
### ETAPA 2: Verificar se a consulta está aguardando recursos

```

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


Os resultados da consulta anterior mostrará o estado de espera da sua solicitação.

- Se a consulta está aguardando em recursos de outra consulta, o estado será **AcquireResources**.
- Se a consulta tem todos os recursos necessários e não está esperando, o estado será **Concedido**. Nesse caso, vá para observar as etapas de consulta.




### ETAPA 3: Localize a etapa mais longa em execução da consulta

Use a ID da solicitação para recuperar uma lista de todas as etapas de consulta distribuídas. Localize a etapa de execução longa observando o tempo total decorrido.

```

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

Use a ID da solicitação e o índice da etapa para recuperar informações sobre a distribuição de consulta do SQL Server como parte da etapa SQL na consulta. Salve a ID de distribuição e o SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Use a seguinte consulta para recuperar o plano de execução do SQL Server para a etapa de SQL em um nó específico.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### ETAPA 4b: localizar o progresso da execução de uma etapa DMS

Use a ID da solicitação e o índice da etapa para recuperar informações sobre a etapa de movimentação de dados em execução em cada distribuição.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Verifique a coluna *total\_elapsed\_time* para ver se uma distribuição específica está demorando muito mais do que outras para movimentar dados. 
- Para a distribuição de longa execução, verifique a coluna *rows\_processed* para verificar se o número de linhas sendo movidas dessa distribuição é significativamente maior do que outros. Isso mostra que a sua consulta tem distorção de dados.





## Investigar distorção de dados

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


O resultado dessa consulta lhe mostrará o número de linhas da tabela que são armazenadas em cada uma das 60 distribuições do seu banco de dados. Para ótimo desempenho, as linhas na tabela distribuída devem ser divididas uniformemente em todas as distribuições. Para saber mais, consulte o [design da tabela][]\:



## Próximas etapas
Para obter mais dicas sobre como gerenciar o SQL Data Warehouse, consulte [Gerenciar visão geral][].

<!--Image references-->

<!--Article references-->
[Gerenciar visão geral]: sql-data-warehouse-overview-manage.md
[design da tabela]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=AcomDC_0107_2016-->