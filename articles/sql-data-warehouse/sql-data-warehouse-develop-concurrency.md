<properties
   pageTitle="Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse | Microsoft Azure"
   description="Compreender o gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse
Para oferecer um desempenho previsível em escala, o SQL Data Warehouse implementa mecanismos para gerenciamento de simultaneidade de carga de trabalho e a atribuição de recursos computacionais.

Este artigo apresenta os conceitos de gerenciamento de simultaneidade e carga de trabalho, explicando como os dois recursos foram implementados e como controlá-los no data warehouse.

## Simultaneidade
É importante entender que a simultaneidade no SQL Data Warehouse é regida pelos dois conceitos: **consultas simultâneas** e **slots de simultaneidade**.

As consultas simultâneas é igual ao número de consultas em execução ao mesmo tempo. O SQL Data Warehouse dá suporte a até 32 **consultas simultâneas**. Cada execução da consulta conta como uma única consulta independentemente de ser uma consulta serial (thread único) ou de consulta paralela (multithread). Esse é um limite fixo e se aplica a todos os níveis de serviço e a todas as consultas.

Slots de simultaneidade é um conceito mais dinâmico e é relativo o objetivo de nível de serviço da DWU (Unidade de Data Warehouse) do data warehouse. À medida que aumenta o número de DWU alocado para o SQL Data Warehouse, mais recursos são atribuídos à computação. No entanto, aumentar DWU também aumenta o número de **slots de simultaneidade** disponíveis.

Como uma regra geral, cada consulta em execução ao mesmo tempo consome um ou mais slots de simultaneidade. O número exato de slots depende de três fatores:

1. A configuração de DWU do SQL Data Warehouse
2. A **classe de recurso** à qual o usuário pertence
3. Se a consulta ou operação é regida pelo modelo de slot de simultaneidade 

> [AZURE.NOTE]Vale a pena observar que nem todas as consultas são regidas pela regra de consulta do slot de simultaneidade. No entanto, a maioria das consultas do usuário é. Algumas consultas e operações não consomem qualquer slot de simultaneidade. Essas consultas e operações ainda são limitadas pelo limite de consultas simultâneas, motivo pelo qual as duas regras são descritas. Consulte a seção [exceções de classe de recurso](#exceptions) abaixo para obter mais detalhes.

A tabela a seguir descreve os limites de consultas simultâneas e slots de simultaneidade, supondo que sua consulta seja controlada por recurso.

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

Cargas de trabalho de consulta do SQL Data Warehouse precisam ficar dentro desses limites. Se houver mais de 32 consultas simultâneas ou se você exceder o número de slots de simultaneidade, a consulta será enfileirada até que ambos os limites possam ser satisfeitos.

## Gerenciamento de carga de trabalho

O SQL Data Warehouse expõe quatro classes de recursos diferente na forma de **funções de banco de dados** como parte de sua implementação de gerenciamento de carga de trabalho.

As funções são:

- smallrc
- mediumrc
- largerc
- xlargerc

Classes de recursos são uma parte essencial do gerenciamento de carga de trabalho do SQL Data Warehouse. Elas controlam os recursos computacionais alocados à consulta.

Por padrão, cada usuário é um membro da pequena classe de recurso - smallrc. No entanto, qualquer usuário pode ser adicionado a uma ou mais das classes com mais recursos. Como regra geral, o SQL Data Warehouse considerará a associação de função mais alta para a execução da consulta. Adicionar um usuário a uma classe de recurso maior aumentará os recursos para o usuário, mas também consumirá mais slots de simultaneidade, potencialmente limitando a simultaneidade. Isso é devido ao fato de que, à medida que mais recursos são alocados para uma consulta, o sistema precisa para limitar os recursos consumidos por outros usuários. Não há nenhum bônus.

O recurso mais importante regido pela classe de recurso maior é a memória. A maioria das tabelas do data warehouse de qualquer tamanho significativo usam índices columnstore clusterizados. Embora isso geralmente forneça o melhor desempenho para cargas de trabalho de data warehouse, mantê-los é uma operação que demanda uso intensivo de memória. Geralmente é muito útil usar as classes de recursos mais elevadas para operações de gerenciamento de dados, como a recriação de índice.

O SQL Data Warehouse implementou classes de recursos com o uso de funções de banco de dados. Para se tornar um membro de uma classe de recurso maior e aumentar sua memória e prioridade, basta adicionar o usuário de banco de dados a uma das funções/classes de recursos mencionadas acima.

### Associação da classe de recurso

Você mesmo pode adicionar e remover a função de banco de dados de gerenciamento de carga de trabalho usando os procedimentos `sp_addrolemember` e `sp_droprolemember`. Observe que você precisará de permissão de `ALTER ROLE` para fazer isso. Não é possível usar a sintaxe ALTER ROLE DDL. Você deve usar os procedimentos armazenados mencionados anteriormente. Um exemplo completo de como criar logons e usuários é apresentado na seção [gerenciando usuários) [#gerenciando-usuários] no final deste artigo.

> [AZURE.NOTE]Em vez de adicionar um usuário dentro e fora de um grupo de gerenciamento de carga de trabalho, costuma ser mais simples iniciar essas operações mais pesadas por meio de um logon/usuário separado permanentemente atribuído à classe de recurso mais elevada.

### Alocação de memória

A tabela a seguir fornece detalhes sobre o aumento de memória disponível para cada consulta, sujeito à classe de recurso aplicada ao usuário que a executa:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| Memória disponível (por dist) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |

### Consumo de slot de simultaneidade

Além disso, conforme mencionado acima, quanto maior for a classe de recurso atribuída ao usuário, maior será o consumo de slot de simultaneidade. A tabela a seguir documenta o consumo de slots de simultaneidade por consultas em uma classe de determinado recurso.

<!--
| Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consumo | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Máximo de consultas simultâneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Máximo de slots simultâneos | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

### Exceções

Há ocasiões em que membros de uma classe de recurso superior não alteram os recursos atribuídos à consulta ou operação. Geralmente, isso ocorre quando os recursos necessários para atender a ação estão baixos. Nesses casos, a classe de recurso padrão ou pequena (smallrc) sempre é usada, não importa a classe de recurso atribuída ao usuário. Por exemplo, `CREATE LOGIN` sempre será executado no smallrc. Os recursos necessários para atender a essa operação são muito baixos, portanto, não faria sentido incluir a consulta no modelo de slot de simultaneidade. Seria um desperdício pré-alocar grandes quantidades de memória para essa ação. Excluir `CREATE LOGIN` do modelo de slot de simultaneidade do SQL Data Warehouse pode ser muito mais eficiente.

Abaixo está uma lista de instruções e operações que **são** regidas por classes de recursos:

- INSERT-SELECT
- UPDATE
- EXCLUIR
- SELECT (ao consultar tabelas de usuário)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT 
- Carregamento de dados 
- Operações de movimentação de dados realizadas pelo Serviço de Movimentação de Dados (DMS)

As instruções a seguir **não** honram classes de recursos:

- CREATE TABLE
- ALTER TABLE ... SWITCH PARTITION 
- ALTER TABLE ... SPLIT PARTITION 
- ALTER TABLE ... MERGE PARTITION 
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT (de exibições do sistema e DMVs)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE 
-->
> [AZURE.NOTE]Vale a pena destacar que consultas `SELECT` em execução exclusivamente em exibições de gerenciamento dinâmico e exibições de catálogo **não** são regidas por classes de recursos.

É importante lembrar-se de que a maioria das consultas do usuário final provavelmente será regida pelas classes de recurso. A regra geral é que a carga de trabalho da consulta ativa deve caber nos limites tanto da consulta simultânea quanto no do slot de simultaneidade, a menos que tenha sido excluída especificamente pela plataforma. Como um usuário final, você não pode optar por excluir uma consulta de modelo de slot de simultaneidade. Assim que um dos limites for excedido, as consultas começarão a ser enfileiradas. Consultas em fila serão tratadas em ordem de prioridade, seguido por hora de envio.

### Elementos internos 

Nos bastidores da carga de trabalho do SQL Data Warehouse, questões de gerenciamento são um pouco mais complicadas. As classes de recurso são mapeadas dinamicamente para um conjunto geral de grupos de gerenciamento de carga de trabalho no administrador de recursos. Os grupos usados dependem do valor DWU para o depósito. No entanto, há um total de oito grupos de carga de trabalho usados pelo SQL Data Warehouse. Eles são:

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

Esses 8 grupos mapeiam entre o consumo de slot de simultaneidade

| Grupo de carga de trabalho | Mapeamento do slot de simultaneidade | Mapeamento de prioridade |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | Média |
| SloDWGroupC01 | 2 | Média |
| SloDWGroupC02 | 4 | Média |
| SloDWGroupC03 | 8 | Média |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Assim, por exemplo, se DW500 é a configuração atual de DWU para o SQL Data Warehouse, os grupos de cargas de trabalho ativos são mapeados para as classes do recurso da seguinte maneira:

| Classe de recurso | Grupo de carga de trabalho | Slots simultâneos usados | Importância |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Média |
| mediumrc | SloDWGroupC02 | 4 | Média |
| largerc | SloDWGroupC03 | 8 | Média |
| xlargerc | SloDWGroupC04 | 16 | Alto |

Para examinar as diferenças na alocação de recursos de memória em detalhes da perspectiva do administrador de recursos, use a seguinte consulta:

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
) 
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY 
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE]A consulta acima também pode ser usada para analisar o uso ativo e histórico dos grupos de carga de trabalho na solução de problemas.

## Exemplos de gerenciamento de carga de trabalho

Esta seção fornece alguns exemplos adicionais para analisar para gerenciar usuários e detectar consultas que estão criando filas.

### Gerenciando usuários

Para conceder acesso a um usuário ao SQL Data Warehouse, será necessário primeiro um logon.

Abra uma conexão com o banco de dados mestre para seu SQL Data Warehouse e execute os seguintes comandos:

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

[AZURE.NOTE]é recomendável criar usuários para seus logons no banco de dados mestre ao trabalhar com o Banco de Dados SQL do Azure e SQL Data Warehouse. Há duas funções de servidor disponíveis nesse nível que exigem que o logon tenha um usuário em mestre para conceder a associação. As funções são `Loginmanager` e `dbmanager`. No Banco de Dados SQL do Azure e SQL Data Warehouse, essas funções concedem direitos para gerenciar logons e criar bancos de dados. Isso é diferente para o SQL Server. Para obter mais detalhes, consulte o artigo [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure].

Depois que o logon for criado, a conta de usuário deve ser adicionada.

Abra uma conexão com o banco de dados do SQL Data Warehouse e execute o seguinte comando:

```
CREATE USER newperson FOR LOGIN newperson
```

Uma vez concluídas as permissões, elas precisarão ser concedidas ao usuário. O exemplo a seguir concede `CONTROL` no banco de dados do SQL Data Warehouse. `CONTROL` no nível do banco de dados é equivalente a db\_owner no SQL Server.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

Para ver as funções de gerenciamento de carga de trabalho, use a seguinte consulta:

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Para adicionar um usuário a uma função de gerenciamento do aumento de cargas de trabalho, use a seguinte consulta:

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

Para remover um usuário de uma função de gerenciamento de carga de trabalho, use a seguinte consulta:

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```

> [AZURE.NOTE]Não é possível remover um usuário de smallrc.

Para ver quais usuários são membros de uma determinada função, use a seguinte consulta:

```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### Detecção de consulta em fila
Para identificar as consultas que são mantidas em uma fila de simultaneidade, você sempre poderá consultar o `sys.dm_pdw_exec_requests` DMV.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

O SQL Data Warehouse tem tipos de espera específico para medir a simultaneidade.

Eles são:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

O LocalQueriesConcurrencyResourceType refere-se às consultas que ficam fora da estrutura do slot de simultaneidade. Consultas DMV e funções de sistema, como `SELECT @@VERSION`, são exemplos de consultas de locais.

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
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->