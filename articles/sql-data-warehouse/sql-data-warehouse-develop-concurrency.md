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
   ms.date="08/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse

Para oferecer um desempenho previsível em escala, o SQL Data Warehouse permite aos usuários controlar os níveis de simultaneidade, bem como as alocações de recursos como memória e priorização de CPU. Este artigo apresenta os conceitos de gerenciamento de simultaneidade e carga de trabalho, explicando como os dois recursos foram implementados e como controlá-los no data warehouse. O gerenciamento de carga de trabalho do SQL Data Warehouse destina-se a ajudá-lo a dar suporte a ambientes com vários usuários. Ele não se destina a cargas de trabalho com vários locatários.

## Limites de simultaneidade

O SQL Data Warehouse permite até 1.024 conexões simultâneas. Todas as 1.024 conexões podem enviar consultas ao mesmo tempo. No entanto, para otimizar a taxa de transferência, o SQL Data Warehouse pode enfileirar algumas consultas para garantir que cada consulta receba uma concessão de memória mínima. O enfileiramento ocorre no tempo de execução de consulta. Enfileirando consultas quando os limites de simultaneidade são atingidos, o SQL Data Warehouse é capaz de aumentar a taxa de transferência total, garantindo que as consultas ativas obtenham acesso aos recursos de memória muito necessários.

Os limites de simultaneidade são regidos por dois conceitos, **consultas simultâneas** e **slots de simultaneidade**. Para a execução de uma consulta, ela deve ser executada tanto no limite de simultaneidade de consulta quanto na alocação de slot de simultaneidade.

- As **consultas simultâneas** são simplesmente o número de consultas em execução ao mesmo tempo. O SQL Data Warehouse dá suporte para até 32 consultas simultâneas.
- Os **slots de simultaneidade** são alocados com base em DWU. Cada DWU 100 fornece quatro slots de simultaneidade. Por exemplo, um DW100 aloca quatro slots de simultaneidade e um DW1000 aloca 40. Cada consulta consome um ou mais slots de simultaneidade, dependendo da [classe de recurso](#resource-classes) da consulta. As consultas em execução na classe de recurso smallrc consomem um slot de simultaneidade. As consultas em execução em uma classe de recurso superior consomem mais slots de simultaneidade.

A tabela abaixo descreve os limites das consultas simultâneas e dos slots de simultaneidade em vários tamanhos de DWU.

### Limites de simultaneidade

| DWU | Máximo de consultas simultâneas | Slots de Simultaneidade Alocados |
| :----  | :---------------------: | :-------------------------: |
| DW100 | 32 | 4 |
| DW200 | 32 | 8 |
| DW300 | 32 | 12 |
| DW400 | 32 | 16 |
| DW500 | 32 | 20 |
| DW600 | 32 | 24 |
| DW1000 | 32 | 40 |
| DW1200 | 32 | 48 |
| DW1500 | 32 | 60 |
| DW2000 | 32 | 80 |
| DW3000 | 32 | 120 |
| DW6000 | 32 | 240 |

Quando um desses limites é atingido, novas consultas são enfileiradas. As consultas enfileiradas são executadas seguindo o método "primeiro a entrar, primeiro a sair ", à medida que outras consultas são concluídas e o número de consultas e slots fica abaixo dos limites.

> [AZURE.NOTE]  As consultas SELECT em execução exclusivamente nas DMVs (exibições de gerenciamento dinâmico) ou nas exibições de catálogo **não** são governadas por nenhum dos limites de simultaneidade. Isso permite aos usuários monitorar o sistema independentemente do número de consultas em execução no sistema.

## Classes de recursos

As classes de recurso são a maneira em que você controla a alocação de memória e os ciclos de CPU fornecidos para uma consulta. Há quatro classes de recurso que podem ser atribuídas a um usuário na forma de uma **função de banco de dados**. As quatro classes de recurso são **smallrc, mediumrc, largerc e xlargerc**. Os usuários em smallrc recebem uma quantidade menor de memória e, assim, podem ter maior simultaneidade. Por outro lado, os usuários atribuídos a xlargerc recebem grandes quantidades de memória e, assim, um número menor dessas consultas pode ser executado simultaneamente.

Por padrão, cada usuário é um membro da pequena classe de recurso - smallrc. O procedimento `sp_addrolemember` é usado para aumentar a classe de recurso e `sp_droprolemember` é usado para diminuir a classe de recurso. Por exemplo, este comando aumentaria a classe de recurso de loaduser para largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Uma prática recomendada é criar usuários que são permanentemente atribuídos a uma classe de recurso, em vez de alterar a classe de recurso de um usuário. Por exemplo, cargas para tabelas columnstore clusterizadas cria índices de qualidade superiores quando mais memória é alocada. Para garantir que as cargas tenham acesso a mais memória, crie um usuário especificamente para o carregamento de dados e atribua esse usuário permanentemente a uma classe de recurso mais elevada.

Existem alguns tipos de consultas que não se beneficiam da alocação de memória maior e o sistema ignorará sua alocação de classe de recurso e sempre executará essas consultas na classe de recurso pequena em vez disso. Forçar essas consultas a serem sempre executadas na classe de recurso pequena permite que elas sejam executadas quando os slots de simultaneidade estão sob pressão e impede que essas consultas consumam mais slots do que o necessário. Essas [exceções de classe de recurso](#query-exceptions-to-concurrency-limits) são abordadas mais adiante neste artigo.

Mais alguns detalhes sobre classes de recurso:

- A permissão `ALTER ROLE` é necessária para alterar a classe de recurso de um usuário.
- Embora um usuário possa ser adicionado a uma ou mais classes com mais recursos, os usuários terão os atributos de classe de recurso mais alta à qual são atribuídos. Ou seja, se um usuário for atribuído a mediumrc e largerc, a classe de recurso mais elevada, largerc, será a classe de recurso que será respeitada.
- A classe de recurso do usuário administrativo do sistema não pode ser alterada.
 
Para obter um exemplo detalhado, confira [Changing user resource class example](#changing-user-resource-class-example) (Alterando o exemplo de classe de recurso de usuário) no final deste artigo

## Alocação de memória

Há prós e contras quanto ao aumento da classe de recurso do usuário. Embora o aumento de uma classe de recurso para um usuário possa significar que suas consultas têm acesso a mais memória e podem ser executadas mais rapidamente, classes de recurso superiores também reduzem o número de consultas simultâneas que podem ser executadas. Essa é a compensação entre alocar grandes quantidades de memória para uma única consulta e permitir que outras consultas, que também precisam de alocações de memória, sejam executadas simultaneamente. Se um usuário receber alocações de memória altas para uma consulta, outros usuários não terão acesso a essa mesma memória para executar uma consulta.

A tabela a seguir mapeia a memória alocada para cada distribuição por DWU e classe de recurso. Há 60 distribuições no SQL Data Warehouse. Por exemplo, uma consulta em execução em um DW2000 na classe de recurso xlarge teria acesso a 6.400 MB de memória dentro de cada um dos 60 bancos de dados distribuídos.

### Alocações de memória por distribuição (MB)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 100 | 100 | 200 | 400 |
| DW200 | 100 | 200 | 400 | 800 |
| DW300 | 100 | 200 | 400 | 800 |
| DW400 | 100 | 400 | 800 | 1\.600 |
| DW500 | 100 | 400 | 800 | 1\.600 |
| DW600 | 100 | 400 | 800 | 1\.600 |
| DW1000 | 100 | 800 | 1\.600 | 3\.200 |
| DW1200 | 100 | 800 | 1\.600 | 3\.200 |
| DW1500 | 100 | 800 | 1\.600 | 3\.200 |
| DW2000 | 100 | 1\.600 | 3\.200 | 6\.400 |
| DW3000 | 100 | 1\.600 | 3\.200 | 6\.400 |
| DW6000 | 100 | 3\.200 | 6\.400 | 12\.800 |

Usando o mesmo exemplo acima, uma consulta em execução em um DW2000 na classe de recurso xlarge recebe a alocação de um total de 375 GB de memória (6.400 MB * 60 distribuições / 1.024 para converter em GB) por todo o SQL Data Warehouse.

### Alocações de memória em todo o sistema (GB)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 6 | 6 | 12 | 23 |
| DW200 | 6 | 12 | 23 | 47 |
| DW300 | 6 | 12 | 23 | 47 |
| DW400 | 6 | 23 | 47 | 94 |
| DW500 | 6 | 23 | 47 | 94 |
| DW600 | 6 | 23 | 47 | 94 |
| DW1000 | 6 | 47 | 94 | 188 |
| DW1200 | 6 | 47 | 94 | 188 |
| DW1500 | 6 | 47 | 94 | 188 |
| DW2000 | 6 | 94 | 188 | 375 |
| DW3000 | 6 | 94 | 188 | 375 |
| DW6000 | 6 | 188 | 375 | 750 |


## Consumo de slot de simultaneidade

As consultas executadas em uma classe de recurso superior recebem mais memória. Como a memória é um recurso fixo, quanto mais memória for alocada por consulta, menos simultaneidade terá suporte. A tabela a seguir reitera todos os conceitos acima em uma única exibição mostrando o número de slots de simultaneidade disponíveis por DWU, bem como os slots consumidos por cada classe de recurso.

### Alocação e consumo de slots de simultaneidade

| DWU | Máximo de consultas simultâneas | Slots de Simultaneidade Alocados | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100 | 32 | 4 | 1 | 1 | 2 | 4 |
| DW200 | 32 | 8 | 1 | 2 | 4 | 8 |
| DW300 | 32 | 12 | 1 | 2 | 4 | 8 |
| DW400 | 32 | 16 | 1 | 4 | 8 | 16 |
| DW500 | 32 | 20 | 1 | 4 | 8 | 16 |
| DW600 | 32 | 24 | 1 | 4 | 8 | 16 |
| DW1000 | 32 | 40 | 1 | 8 | 16 | 32 |
| DW1200 | 32 | 48 | 1 | 8 | 16 | 32 |
| DW1500 | 32 | 60 | 1 | 8 | 16 | 32 |
| DW2000 | 32 | 80 | 1 | 16 | 32 | 64 |
| DW3000 | 32 | 120 | 1 | 16 | 32 | 64 |
| DW6000 | 32 | 240 | 1 | 32 | 64 | 128 |


Nesta tabela, você pode ver que um SQL Data Warehouse em execução como DW1000 aloca uma quantidade máxima de 32 consultas simultâneas e um total de 40 slots de simultaneidade. Se todos os usuários estivessem executando na classe de recurso pequena, seriam permitidas 32 consultas simultâneas, uma vez que cada consulta consumiria um slot de simultaneidade. Se todos os usuários em um DW1000 estivessem executando na classe de recurso média, cada consulta receberia a alocação de 800 MB por distribuição de uma alocação de memória total de 47 GB por consulta e a simultaneidade seria limitada a cinco usuários (40 slots de simultaneidade/oito slots por usuário mediumrc).

## Importância da consulta

De modo oculto, as classes de recurso são implementadas usando grupos de carga de trabalho. Há um total de oito grupos de carga de trabalho que controlam o comportamento das classes de recurso entre os vários tamanhos de DWU. Em qualquer DWU, apenas quatro dos oito grupos de carga de trabalho são utilizados. Isso faz sentido, uma vez que cada grupo de carga de trabalho é atribuído a uma das quatro classes de recurso: smallrc, mediumrc, largerc ou xlargerc. A importância de entender esses grupos de carga de trabalho é que alguns deles são definidos com **IMPORTÂNCIA** maior. A importância é usada para agendamento de CPU. As consultas executadas com importância **alta** obterão três vezes mais ciclos de CPU do que aquelas com importância **média**. Portanto, os mapeamentos de slot de simultaneidade também determinam a prioridade da CPU. Quando uma consulta consome 16 ou mais slots, ela é executada com alta importância.

A seguir estão os mapeamentos de importância para cada grupo de carga de trabalho.

### Mapeamentos de grupo de carga de trabalho para slots de simultaneidade e importância

| Grupos de carga de trabalho | Mapeamento do slot de simultaneidade | Mapeamento de Importância |
| :-------------- | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Média |
| SloDWGroupC01 | 2 | Média |
| SloDWGroupC02 | 4 | Média |
| SloDWGroupC03 | 8 | Média |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Do gráfico de **Alocação e consumo de slots de simultaneidade**, podemos ver que um DW500 usa um, quatro, oito ou 16 slots de simultaneidade para smallrc, mediumrc, largerc e xlargerc respectivamente. É possível procurar por esses valores no gráfico acima para obter a importância de cada classe de recurso.

### Mapeamento do DW500 para obter a importância das classes de recurso

| Classe de recursos | Grupo de carga de trabalho | Slots simultâneos usados | Importância |
| :------------- | :------------- | :--------------------: | :--------- |
| smallrc | SloDWGroupC00 | 1 | Média |
| mediumrc | SloDWGroupC02 | 4 | Média |
| largerc | SloDWGroupC03 | 8 | Média |
| xlargerc | SloDWGroupC04 | 16 | Alto |


A seguinte consulta DMV pode ser usada para examinar as diferenças na alocação de recursos de memória em detalhes da perspectiva do administrador de recursos ou para analisar o uso ativo e histórico dos grupos de carga de trabalho ao solucionar problemas:

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
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

## Consultas que respeitam os limites de simultaneidade

A maioria das consultas é governada pelas classes de recurso. Essas consultas devem se ajustar tanto aos limites de consultas de simultaneidade quanto aos de slots de simultaneidade. Um usuário final não pode optar por excluir uma consulta do modelo de slot de simultaneidade.

Para reiterar, estas instruções **seguem** as classes de recurso:

- INSERT-SELECT
- UPDATE
- EXCLUIR
- SELECT (ao consultar tabelas de usuário)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Carregamento de dados
- Operações de movimentação de dados realizadas pelo Serviço de Movimentação de Dados (DMS)

## Exceções de consulta para limites de simultaneidade

Há algumas consultas que não respeitam a classe de recurso à qual o usuário é atribuído. Essas exceções para os limites de simultaneidade são feitas quando os recursos de memória necessários para um determinado comando estão baixos, geralmente porque o comando é uma operação de metadados. Ao ter essas exceções, as alocações maiores de memória em consultas que jamais precisarão delas são evitadas. Nesses casos, a classe de recurso padrão pequena (smallrc) sempre é usada, independentemente da classe de recurso real atribuída ao usuário. Por exemplo, `CREATE LOGIN` sempre será executado no smallrc. Os recursos necessários para atender a essa operação são muito baixos, portanto, não faria sentido incluir a consulta no modelo de slot de simultaneidade. Seria um desperdício pré-alocar grandes quantidades de memória para essa ação. Excluir `CREATE LOGIN` do modelo de slot de simultaneidade do SQL Data Warehouse pode ser muito mais eficiente.

As instruções a seguir **não** honram classes de recursos:

- CREATE ou DROP TABLE
- ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE ou DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER ou DROP USER
- CREATE, ALTER ou DROP PROCEDURE
- CREATE ou DROP VIEW
- INSERT VALUES
- SELECT de exibições do sistema e DMVs
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## Alteração do exemplo de classe de recurso de usuário

1. **Criar logon:** abra uma conexão com o banco de dados **mestre** para o SQL Data Warehouse e execute os comandos a seguir.
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] É recomendável criar usuários para logons no banco de dados mestre, no banco de dados SQL do Azure e no Azure SQL Data Warehouse. Há duas funções de servidor disponíveis nesse nível que exigem que o logon tenha um usuário em mestre para conceder a associação. As funções são `Loginmanager` e `dbmanager`. No Banco de Dados SQL do Azure e SQL Data Warehouse, essas funções concedem direitos para gerenciar logons e criar bancos de dados. Isso é diferente para o SQL Server. Para obter mais detalhes, confira o artigo [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure][].

2. **Criar conta de usuário:** abra uma conexão com o **banco de dados SQL Data Warehouse** e execute o comando a seguir.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Conceder permissões:** o exemplo a seguir concede `CONTROL` no banco de dados SQL Data Warehouse. `CONTROL` no nível de banco de dados é o equivalente a db\_owner no SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Aumentar a classe de recurso:** para adicionar um usuário a uma função de gerenciamento de aumento de carga de trabalho, use a consulta a seguir.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Diminuir a classe de recurso:** para remover um usuário de uma função de gerenciamento de carga de trabalho, use a consulta a seguir.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] Não é possível remover um usuário de smallrc.

## Detecção de consulta enfileirada e outros DMVs

O DMV `sys.dm_pdw_exec_requests` pode ser usado para identificar as consultas que estão aguardando em uma fila de simultaneidade. As consultas que estão aguardando um slot de simultaneidade terão um status de **suspensas**.

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

As funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A consulta a seguir mostra a qual função cada usuário está atribuído.

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

O SQL Data Warehouse tem os tipos de espera a seguir.

- LocalQueriesConcurrencyResourceType: consultas que ficam fora da estrutura de slot de simultaneidade. Consultas DMV e funções de sistema, como `SELECT @@VERSION`, são exemplos de consultas de locais.
- UserConcurrencyResourceType: consultas que ficam dentro da estrutura de slot de simultaneidade. Consultas em tabelas do usuário final representam exemplos que usam esse tipo de recurso.
- DmsConcurrencyResourceType: esperas resultantes de operações de movimentação de dados
- BackupConcurrencyResourceType: essa espera indica que está sendo feito backup de um banco de dados. O valor máximo para esse tipo de recurso é 1. Se vários backups foram solicitados ao mesmo tempo, os outros serão colocados em fila.

O DMV `sys.dm_pdw_waits` pode ser usado para ver quais recursos uma solicitação está aguardando.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
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
WHERE	[session_id] <> SESSION_ID();
```

O DMV `sys.dm_pdw_wait_stats` pode ser usado para análise de tendências históricas de espera.

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## Próximas etapas

Para obter mais informações sobre como gerenciar usuários de banco de dados e segurança, confira [Proteger um banco de dados no SQL Data Warehouse][]. Para saber mais sobre como classes de recurso maiores podem melhorar a qualidade do índice columnstore clusterizado, confira [Recriar índices para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Proteger um banco de dados no SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Recriar índices para melhorar a qualidade de segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0803_2016-->