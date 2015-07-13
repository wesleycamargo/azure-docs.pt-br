<properties
   pageTitle="Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse | Microsoft Azure"
   description="Compreender o gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse
Para oferecer um desempenho previsível em escala, o SQL Data Warehouse implementa mecanismos para gerenciamento de simultaneidade de carga de trabalho e a atribuição de recursos computacionais.

Este artigo apresenta os conceitos de gerenciamento de simultaneidade e carga de trabalho, explicando como os dois recursos foram implementados e como controlá-los no data warehouse.

## Simultaneidade
É importante entender que a simultaneidade no SQL Data Warehouse é regida pelos dois conceitos: **consultas simultâneas** e **slots de simultaneidade**.

As consultas simultâneas é igual ao número de consultas em execução ao mesmo tempo. O SQL Data Warehouse dá suporte a até 32 **consultas simultâneas**. Cada execução da consulta conta como uma única consulta independentemente de ser uma consulta serial (thread único) ou de consulta paralela (multithread). Esse é um limite fixo e se aplica a todos os níveis de serviço.

Slots de simultaneidade é um conceito mais dinâmico e é relativo o objetivo de nível de serviço da DWU (Unidade de Data Warehouse) do data warehouse. À medida que aumenta o número de DWU alocado para o SQL Data Warehouse, mais recursos são atribuídos à computação. No entanto, aumentar DWU também aumenta o número de **slots de simultaneidade** disponíveis.

O SQL Data Warehouse convive com ambos os limites. Se houver mais de 32 consultas simultâneas ou se você exceder o número de slots de simultaneidade, a consulta será enfileirada até que ambos os limites possam ser satisfeitos.

Executar cada consulta simultaneamente consome um ou mais slots de simultaneidade. O número exato de slots depende de dois fatores:

1. A configuração de DWU do SQL Data Warehouse
2. A **classe de recurso** à qual o usuário pertence 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Consumo de slot de simultaneidade | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| Máximo de consultas simultâneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| Máximo de slots simultâneos | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

Classes de recursos são uma parte essencial do gerenciamento de carga de trabalho do SQL Data Warehouse, pois também definem recursos computacionais alocados para a consulta. Elas serão abordadas na seção de gerenciamento de carga de trabalho abaixo.

## Gerenciamento de carga de trabalho

O SQL Data Warehouse expõe quatro classes de recursos diferente na forma de **funções de banco de dados** como parte de sua implementação de gerenciamento de carga de trabalho.

As funções são:

- smallrc
- mediumrc
- largerc
- xlargerc

Você mesmo pode ver as funções com a consulta a seguir.

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Por padrão, cada usuário é um membro da classe de recurso pequena - smallrc. No entanto, qualquer usuário pode ser adicionado a uma ou mais das classes com mais recursos. O SQL Data Warehouse considerará a associação de função mais alta para a execução da consulta. Adicionar um usuário a uma classe de recurso maior aumentará os recursos para o usuário, mas também consumirá mais slots de simultaneidade, potencialmente limitando a simultaneidade. Isso é devido ao fato de que, à medida que mais recursos são alocados para uma consulta, o sistema precisa para limitar os recursos consumidos por outros usuários. Não há nenhum bônus.

O recurso mais importante regido pela classe de recurso maior é a memória. A maioria das tabelas do data warehouse de qualquer tamanho significativo usam índices columnstore clusterizados. Embora isso geralmente forneça o melhor desempenho para cargas de trabalho de data warehouse, mantê-los é uma operação que demanda uso intensivo de memória. Geralmente é muito útil usar as classes de recursos mais elevadas para operações de gerenciamento de dados, como a recriação de índice.

Para aumentar a memória, basta adicionar o usuário de banco de dados a uma das funções mencionadas acima.

Você mesmo pode adicionar e remover a função de banco de dados de gerenciamento de carga de trabalho usando os procedimentos `sp_addrolemember` e `sp_droprolemember`. Observe que você precisará de permissão de `ALTER ROLE` para fazer isso. Não é possível usar a sintaxe ALTER ROLE DDL. Você deve usar os procedimentos armazenados mencionados anteriormente.

> [AZURE.NOTE]Em vez de adicionar um usuário dentro e fora de um grupo de gerenciamento de carga de trabalho, costuma ser mais simples iniciar essas operações mais pesadas por meio de um logon/usuário separado permanentemente atribuído à classe de recurso mais elevada.

A tabela a seguir fornece detalhes sobre o aumento de memória disponível para cada consulta, sujeito à classe de recurso aplicada ao usuário que a executa:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->
| Memória disponível (por dist) | Prioridade | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | Média | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | Média | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | Alto | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | Alto | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |


Além disso, conforme mencionado acima, quanto maior for a classe de recurso atribuída ao usuário, maior será o consumo de slot de simultaneidade. A tabela a seguir documenta o consumo de slots de simultaneidade por consultas em uma classe de determinado recurso.

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consumo de slot de simultaneidade | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Máximo de consultas simultâneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Máximo de slots simultâneos | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

É importante lembrar de que a carga de trabalho de consulta ativa deve caber em ambos os limites de consulta simultânea e simultaneidade slot. Assim que um dos limites for excedido, as consultas começarão a ser enfileiradas. Consultas em fila serão tratadas em ordem de prioridade, seguido por hora de envio.

## Detecção de consulta em fila
Para identificar as consultas que são mantidas em uma fila de simultaneidade, você sempre poderá consultar o `sys.dm_pdw_exec_requests` DMV.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
FROM    sys.dm_pdw_exec_requests r
;
```

O SQL Data Warehouse tem tipos de espera específico para medir a simultaneidade.

Eles são:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

O LocalQueriesConcurrencyResourceType refere-se às consultas que ficam fora da estrutura do slot de simultaneidade. Consultas DMV e funções do sistema, tais como SELECT @@VERSION, são exemplos de localqueries.

O UserConcurrencyResourceType refere-se às consultas que ficam dentro da estrutura do slot de simultaneidade. Consultas em tabelas do usuário final representam exemplos que usam esse tipo de recurso.

O DmsConcurrencyResourceType refere-se a esperas resultantes de operações de movimentação de dados.

O BackupConcurrencyResourceType pode ser visto quando um banco de dados está passando por backup. O valor máximo para esse tipo de recurso é 1. Se vários backups foram solicitados ao mesmo tempo, os outros serão colocados em fila.


Para executar a análise das consultas atualmente na fila a fim de descobrir quais recursos uma solicitação está aguardando, consulte o `sys.dm_pdw_waits` DMV.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Para exibir apenas as esperas de recursos consumidas por uma determinada consulta, consulte o `sys.dm_pdw_resource_waits` DMV. O tempo de espera do recurso mede apenas o tempo de espera dos recursos a serem fornecidos, não o tempo de espera do sinal, que é o tempo necessário para o SQL Server subjacente agendar a consulta para a CPU.

```
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
WHERE	[session_id] <> SESSION_ID()
;
```

Por fim, para análise de tendências históricas de espera, o SQL Datawarehouse fornece o `sys.dm_pdw_wait_stats` DMV.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte a [visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->