<properties
   pageTitle="Gerenciando distorções de distribuição da tabela | Microsoft Azure"
   description="Diretrizes para ajudar os usuários a identificar a distorção da distribuição em suas tabelas distribuídas"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gerenciando índices columnstore
Os índices columnstore são o suporte principal do Azure SQL Data Warehouse. Ao manter os índices em um bom estado, você maximiza o desempenho do sistema.

Este artigo explica como interrogar os metadados do índice columnstore para suas tabelas; ajudando você a diagnosticar problemas e resolvê-los rapidamente.

## Consultando metadados Columnstore
Para entender a densidade do índice columnstore, é preciso indexar uma consulta aos metadados do sistema. Veja abaixo um exemplo do tipo de informação que você pode descobrir.

```
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

Depois que a exibição tiver sido criada, os metadados columnstore poderão serão facilmente analisados. Uma consulta de exemplo é fornecida abaixo.

```
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Aprimorando a densidade de columnstore
Depois de executar a consulta, você poderá começar a analisar os dados e seus resultados.

Há vários itens a serem observados:

| Coluna | Como usar esses dados |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Se a tabela for particionada, espere ver contagens mais altas de grupos de linhas abertos. Cada partição na distribuição poderia, na teoria, ter um grupo de linhas aberto associado a ela. Fatore isso na sua análise. Uma pequena tabela que foi particionada poderia ser otimizada com a remoção completa do particionamento, pois isso aprimoraria a compactação. |
| [row\_count\_total] | Contagem total de linhas da tabela; esse valor pode ser usado para calcular a porcentagem de linhas no estado compactado, por exemplo |
| [row\_count\_per\_distribution\_MAX] | Se todas as linhas forem distribuídas uniformemente, esse valor será o número alvo de linhas por distribuição. Compare esse valor com compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Número total de linhas no formato columnstore da tabela |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Se o número médio de linhas for significativamente menor do que o número máximo de linhas para um grupo de linhas, considere usar CTAS ou ALTER INDEX REBUILD para compactar novamente os dados |
| [COMPRESSED\_rowgroup\_count] | Número de grupos de linhas no formato columnstore. Se esse número for muito alto em relação à tabela, isso significa que a densidade de columnstore é baixa. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Linhas são excluídas de forma lógica no formato columnstore. Se o número for alto em relação ao tamanho da tabela, considere recriar a partição ou refazer o índice, pois isso as remove fisicamente. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Use em conjunto com as colunas AVG e MAX para entender o intervalo de valores dos grupos de linhas no columnstore. Um número baixo acima do limite de carga (102.400 por distribuição alinhada por partição) sugere que as otimizações estão disponíveis na carga de dados |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Como acima |
| [OPEN\_rowgroup\_count] | Grupos de linhas abertos são normais. Seria razoável esperar um grupo de linhas ABERTO de acordo com a distribuição de tabela (60). Números excessivos sugerem carregamento de dados nas partições. Verifique a estratégia de particionamento para garantir que ela esteja correta |
| [OPEN\_rowgroup\_rows] | Cada grupo de linhas pode ter 1.048.576 linhas, no máximo. Use esse valor para ver quão completo estão os grupos de linhas no momento |
| [OPEN\_rowgroup\_rows\_MIN] | Os grupos abertos indicam que os dados estão sendo carregados lentamente na tabela ou que a carga anterior foi despejada sobre as linhas restantes nesse grupo de linhas. Use as colunas MIN, MAX, AVG para ver a quantidade de dados colocada nos grupos de linhas ABERTOS. Em tabelas pequenas, esse valor pode ser de 100% de dados! Nesse caso, use ALTER INDEX REBUILD para forçar os dados para columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | Como acima |
| [OPEN\_rowgroup\_rows\_AVG] | Como acima |
| [CLOSED\_rowgroup\_rows] | Analise as linhas do grupo de linhas fechado como uma verificação de integridade. Se houver |
| [CLOSED\_rowgroup\_count] | O número de grupos de linhas fechados deverá ser baixo se algum for visto. Os grupos de linhas fechados podem ser convertidos em grupos de linhas compactados usando a instrução ALTER INDEX... Comando REORGANISE. No entanto, normalmente isso não é obrigatório. Os grupos fechados são convertidos automaticamente em grupos de linhas columnstore pelo processo em segundo plano "motor de tupla". |
| [CLOSED\_rowgroup\_rows\_MIN] | Os grupos de linhas fechados devem ter uma taxa de preenchimento alta. Se a taxa de preenchimento de um grupo de linhas fechado for baixa, será necessário fazer outra análise do columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | Como acima |
| [CLOSED\_rowgroup\_rows\_AVG] | Como acima |

## Gerenciamento de índice
É possível recompactar o grupo de linhas criando partições usando [CTAS][] ou refazendo o índice em si usando [ALTER INDEX][]. A execução de [CTAS][] geralmente é mais rápida do que [ALTER INDEX][], especialmente para partições ou tabelas grandes. No entanto, para partições ou tabelas menores, [ALTER INDEX][] geralmente é muito mais conveniente.

>[AZURE.NOTE] ALTER INDEX...REBUILD é uma operação offline. ALTER INDEX...REORGANISE é uma operação online. No entanto, REORGANISE não toca em grupos de linhas abertos. Para incluir grupos de linhas abertos, ALTER INDEX...REBUILD é necessária.

Na recriação de índices, especialmente as tabelas grandes, muitas vezes se beneficiam dos recursos adicionais. O Azure SQL Data Warehouse tem um recurso de gerenciamento de carga de trabalho que pode ser usado para alocar mais memória para um usuário. Para entender como reservar mais memória para recriações de índice, veja a seção de gerenciamento de carga de trabalho do artigo sobre [simultaneidade][].

## Próximas etapas
Para obter mais detalhes sobre como recriar partições usando `CTAS`, veja os seguintes artigos:

* [Partições de tabela][]
* [simultaneidade][]

Para obter aconselhamento detalhado sobre gerenciamento de índices, leia o artigo sobre como [gerenciar índices][].

Para obter mais dicas de gerenciamento, leia uma visão geral sobre [gerenciamento][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Partições de tabela]: sql-data-warehouse-develop-table-partitions.md
[simultaneidade]: sql-data-warehouse-develop-concurrency.md
[gerenciamento]: sql-data-warehouse-manage-monitor.md
[gerenciar índices]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/pt-BR/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->