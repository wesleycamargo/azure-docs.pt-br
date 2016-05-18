<properties
   pageTitle="Gerenciar o tamanho de tabela no Azure SQL Data Warehouse | Microsoft Azure"
   description="Descobrir o tamanho de suas tabelas e partições." 
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
   ms.date="04/13/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gerenciar o tamanho de tabela no Azure SQL Data Warehouse

Este artigo identifica o tamanho de suas partições de tabela e ajuda você a entender o tamanho do banco de dados.


## Etapa 1: Criar uma exibição que resume o tamanho da tabela

Crie esta exibição para identificar quais tabelas têm distorção de dados.

```sql
CREATE VIEW dbo.vPartitionSizing
AS

WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)																AS build_number
,	DB_NAME()																				AS database_name
,	s.name																					AS schema_name
,	t.name																					AS table_name
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)													AS two_part_name
,	nt.name																					AS node_table_name
,   i.[type]                                                                                AS table_type
,   i.[type_desc]                                                                           AS table_type_desc
,	tp.distribution_policy_desc	                        									AS distribution_policy_name
,	pn.pdw_node_id                                                                          AS pdw_node_id
,	pn.[type]																				AS pdw_node_type
,	pn.name																					AS pdw_node_name
,   di.distribution_id                                                                      AS dist_id
,   di.name                                                                                 AS dist_name
,   di.position                                                                             AS dist_position
,	nps.partition_number																	AS partition_nmbr
,	nps.reserved_page_count																	AS reserved_space_page_count
,	nps.reserved_page_count - nps.used_page_count											AS unused_space_page_count
,	nps.in_row_data_page_count + nps.row_overflow_used_page_count + nps.lob_used_page_count	AS  data_space_page_count
,	nps.reserved_page_count 
	- (nps.reserved_page_count - nps.used_page_count) 
	- (in_row_data_page_count+row_overflow_used_page_count+lob_used_page_count)				AS index_space_page_count
,	nps.[row_count]                                                                         AS row_count
from sys.schemas s
join sys.tables t								ON	s.schema_id			= t.schema_id
JOIN sys.indexes i                              ON  t.object_id         = i.object_id
                                                AND i.index_id          <= 1
join sys.pdw_table_distribution_properties	tp	ON	t.object_id			= tp.object_id
join sys.pdw_table_mappings tm					ON	t.object_id			= tm.object_id
join sys.pdw_nodes_tables nt					ON	tm.physical_name	= nt.name
join sys.dm_pdw_nodes pn 						ON  nt.pdw_node_id		= pn.pdw_node_id
join sys.pdw_distributions di                   ON  nt.distribution_id  = di.distribution_id
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.object_id		= nps.object_id
												AND nt.pdw_node_id		= nps.pdw_node_id
                                                AND nt.distribution_id  = nps.distribution_id
)
, size
AS
(
SELECT	build_number
,		database_name
,		schema_name
,		table_name
,		two_part_name
,		node_table_name
,       table_type
,       table_type_desc
,		distribution_policy_name
,       dist_id
,       dist_name
,       dist_position
,		pdw_node_id
,		pdw_node_type
,		pdw_node_name
,		partition_nmbr
,		reserved_space_page_count
,		unused_space_page_count
,		data_space_page_count
,		index_space_page_count
,		row_count
,		(reserved_space_page_count * 8.0)				AS reserved_space_KB
,		(reserved_space_page_count * 8.0)/1000			AS reserved_space_MB
,		(reserved_space_page_count * 8.0)/1000000		AS reserved_space_GB
,		(reserved_space_page_count * 8.0)/1000000000	AS reserved_space_TB
,		(unused_space_page_count * 8.0)					AS unused_space_KB
,		(unused_space_page_count * 8.0)/1000			AS unused_space_MB
,		(unused_space_page_count * 8.0)/1000000			AS unused_space_GB
,		(unused_space_page_count * 8.0)/1000000000		AS unused_space_TB
,		(data_space_page_count * 8.0)					AS data_space_KB
,		(data_space_page_count * 8.0)/1000				AS data_space_MB
,		(data_space_page_count * 8.0)/1000000			AS data_space_GB
,		(data_space_page_count * 8.0)/1000000000		AS data_space_TB
,		(index_space_page_count * 8.0)	    			AS index_space_KB
,		(index_space_page_count * 8.0)/1000				AS index_space_MB
,		(index_space_page_count * 8.0)/1000000			AS index_space_GB
,		(index_space_page_count * 8.0)/1000000000		AS index_space_TB
FROM	base
)
SELECT	* 
FROM	size
;
```

## Etapa 2: Consultar o modo de exibição

Agora que você criou o modo de exibição, execute essas consultas de exemplo para saber mais sobre o tamanho das tabelas:

### Resumo do espaço de tabela usado no banco de dados

```sql
SELECT
	database_name
,	SUM(row_count)				as total_row_count
,	SUM(reserved_space_MB)		as total_reserved_space_MB
,	SUM(data_space_MB)			as total_data_space_MB
,	SUM(index_space_MB)			as total_index_space_MB
,	SUM(unused_space_MB)		as total_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY database_name
;
```

### Espaço de tabela usado pela tabela

```sql
SELECT 
	two_part_name
,	SUM(row_count)				as table_row_count
,	SUM(reserved_space_GB)		as table_reserved_space_GB
,	SUM(data_space_GB)			as table_data_space_GB
,	SUM(index_space_GB)			as table_index_space_GB
,	SUM(unused_space_GB)		as table_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY two_part_name
;
```

### Espaço de tabela por tipo de geometria de tabela

```sql
SELECT 
   table_type_desc
,	SUM(row_count)				as table_type_row_count
,	SUM(reserved_space_GB)		as table_type_reserved_space_GB
,	SUM(data_space_GB)			as table_type_data_space_GB
,	SUM(index_space_GB)			as table_type_index_space_GB
,	SUM(unused_space_GB)		as table_type_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY table_type_desc
;
```

### Espaço de tabela por distribuição

```sql
SELECT 
	pdw_node_id
,	distribution_policy_name
,	dist_name
,	SUM(row_count)				as total_node_distribution_row_count
,	SUM(reserved_space_MB)		as total_node_distribution_reserved_space_MB
,	SUM(data_space_MB)			as total_node_distribution_data_space_MB
,	SUM(index_space_MB)			as total_node_distribution_index_space_MB
,	SUM(unused_space_MB)		as total_node_distribution_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY 	distribution_policy_name
,			dist_name
,			pdw_node_id
ORDER BY    pdw_node_id
,           distribution_policy_name
,		    dist_name
;
```

## Etapa 3: Gerenciar as tabelas

Você encontrou algumas tabelas de que não precisa mais? Considere descartá-las ou exportá-las para o Armazenamento de Blobs usando [CREATE EXTERNAL TABLE AS SELECT (CETAS)][]

Algumas tabelas são maiores que você pensava? Pode ser útil particioná-las. Consulte o artigo em [partições de tabelas][] para obter mais informações.

Você está atingindo o tamanho máximo configurado do seu banco de dados? Use [ALTER DATABASE][] para alterá-lo.

## Próximas etapas
Para obter mais detalhes sobre a distribuição da tabela, consulte os seguintes artigos:

* [Ao design da tabela][]
* [Distribuição hash][]

<!--Image references-->

<!--Article references-->
[Ao design da tabela]: sql-data-warehouse-develop-table-design.md
[Distribuição hash]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->