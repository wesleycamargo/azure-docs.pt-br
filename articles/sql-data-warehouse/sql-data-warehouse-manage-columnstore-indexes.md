<properties
   pageTitle="Gerenciar os índices columnstore no Azure SQL Data Warehouse | Microsoft Azure"
   description="Tutorial de gerenciamento de índices columnstore para melhorar o desempenho de consulta e de compactação no Azure SQL Data Warehouse."
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
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gerenciar os índices columnstore no Azure SQL Data Warehouse.

Este tutorial explica como gerenciar os índices columnstore para melhorar o desempenho das consultas.

As colunas nos índices columnstore funcionam melhor quando o índice comprime as linhas em "rowgroups" de um milhão de linhas (1.048.576 para ser mais exato). Isso proporciona a melhor compactação e o melhor desempenho de consulta. No entanto, algumas condições podem fazer com que os rowgroups tenham uma quantidade consideravelmente menor do que um milhão de linhas. Quando os rowgroups não são densamente preenchidos com linhas, considere alguns ajustes.

Neste tutorial, você aprenderá a:

- Usar metadados para determinar o número médio de linhas por rowgroup
- Considerar a causa raiz para rowgroups esparsamente preenchidos
- Recriar um índice columnstore para compactar novamente todas as linhas em novos rowgroups 

Para aprender as noções básicas dos índices columnstore, confira [Guia sobre columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## Etapa 1: Criar um modo de exibição que mostra metadados do rowgroup

Esse modo de exibição calcula a média de linhas por rowgroup. Ele também mostra outras informações sobre os rowgroups.

```sql
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

## Etapa 2: Consultar o modo de exibição

Agora que você criou o modo de exibição, execute esta consulta de exemplo para ver os metadados do rowgroup para o índice columnstore.

```sql
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

## Etapa 3: Analisar os resultados

Depois de executar a consulta, você poderá começar a analisar os dados e seus resultados. Esta tabela explica o que procurar em sua análise do rowgroup.


| Coluna | Como usar esses dados |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Se a tabela for particionada, espere ver contagens mais altas de grupos de linhas abertos. Cada partição na distribuição poderia, na teoria, ter um grupo de linhas aberto associado a ela. Fatore isso na sua análise. Uma pequena tabela que foi particionada poderia ser otimizada com a remoção completa do particionamento, pois isso aprimoraria a compactação. |
| [row\_count\_total] | Contagem total de linhas para a tabela. Por exemplo, você pode usar este valor para calcular a porcentagem de linhas no estado compactado. |
| [row\_count\_per\_distribution\_MAX] | Se todas as linhas forem distribuídas uniformemente, esse valor será o número alvo de linhas por distribuição. Compare esse valor com compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Número total de linhas no formato columnstore para a tabela. |
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
| [CLOSED\_rowgroup\_rows] | Analise as linhas do grupo de linhas fechado como uma verificação de integridade. |
| [CLOSED\_rowgroup\_count] | O número de grupos de linhas fechados deverá ser baixo se algum for visto. Os grupos de linhas fechados podem ser convertidos em grupos de linhas compactados usando a instrução ALTER INDEX... Comando REORGANISE. No entanto, normalmente isso não é obrigatório. Os grupos fechados são convertidos automaticamente em grupos de linhas columnstore pelo processo em segundo plano "motor de tupla". |
| [CLOSED\_rowgroup\_rows\_MIN] | Os grupos de linhas fechados devem ter uma taxa de preenchimento alta. Se a taxa de preenchimento de um grupo de linhas fechado for baixa, será necessário fazer outra análise do columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | Como acima |
| [CLOSED\_rowgroup\_rows\_AVG] | Como acima |


## Etapa 4: Examinar a causa raiz

O exame da causa raiz ajuda você a saber se é possível fazer o design da tabela, o carregamento ou outras alterações no processo que aprimorarão a densidade da linha em seus rowgroups; melhorando o desempenho da compactação e da consulta.

Esses fatores podem fazer com que um índice columnstore tenha uma quantidade consideravelmente menor do que 1.048.576 linhas por cada rowgroup. Eles também podem colocar as linhas no rowgroup delta, em vez do rowgroup compactado.

1. Operações DML pesadas
2. Operações de carregamento pequenas ou lentas
3. Número excessivo de partições

### Operações DML pesadas** 

Operações DML pesadas que atualizam e excluem linhas causam ineficiência ao columnstore. Isso acontece principalmente quando a maioria das linhas em um rowgroup é modificada.

- A exclusão de uma linha de um rowgroup compactado somente marca logicamente a linha como excluída. A linha permanece no rowgroup compactado até que a partição ou a tabela seja recriada.
- A inserção de uma linha a adiciona a uma tabela interna da rowstore chamada de rowgroup delta. A linha inserida não é convertida para a columnstore, até que o rowgroup delta esteja cheio e marcado como fechado. Rowgroups são fechados quando atingem a capacidade máxima de 1.048.576 linhas. 
- A atualização de uma linha no formato columnstore é processada como uma exclusão lógica e, em seguida, como uma inserção. A linha inserida será armazenada no repositório delta.

As operações de atualização e inserção em lote que excedem o limite em massa de 102.400 linhas por distribuição alinhada em partição serão gravadas diretamente no formato columnstore. No entanto, supondo a ocorrência de uma distribuição uniforme, seria necessário modificar mais de 6.144 milhões de linhas em uma única operação para que isso ocorresse. Se o número de linhas de uma determinada distribuição alinhada em partição for menor do que 102.400, as linhas serão encaminhadas ao repositório delta e permanecerão lá até que uma quantidade suficiente de linhas seja inserida ou modificada, a fim de fechar o rowgroup ou recriar o índice.

### Operações de carregamento pequenas ou lentas

Às vezes, pequenas cargas que fluem para o SQL Data Warehouse também são chamadas de cargas lentas. Normalmente, elas representam um fluxo quase constante de dados que estão sendo incluídos pelo sistema. No entanto, como esse fluxo é quase contínuo, o volume de linhas não é grande. Frequentemente, os dados ficam consideravelmente abaixo do limite necessário para um carregamento direto no formato columnstore.

Nessas situações, é melhor levar os dados primeiro ao armazenamento de blobs do Azure e deixá-los se acumularem antes do carregamento. Essa técnica é conhecida normalmente como *micro envio em lote*.

### Número excessivo de partições

Talvez você tenha muitas partições. Na arquitetura MPP (processamento paralelo massivo), uma tabela definida pelo usuário é distribuída e implementada como 60 tabelas nos bastidores. Portanto, cada índice columnstore é implementado como 60 índices columnstore. Da mesma forma, cada partição é implementada nesses 60 índices columnstore. Essa é uma **grande diferença** do SQL Server, que tem uma arquitetura SMP (multiprocessamento simétrico).

Se você carregar 1.048.576 linhas em uma partição de uma tabela SMP SQL Server, ela será compactada no columnstore. No entanto, se você carregar 1.048.576 linhas em uma partição de uma tabela do SQL Data Warehouse, apenas 17.467 linhas serão levadas a cada uma das 60 distribuições (supondo uma distribuição uniforme dos dados). Como 17.467 é inferior ao limite de 102.400 linhas por distribuição, o SQL Data Warehouse armazenará os dados em rowgroups deltastore. Portanto, para carregar linhas diretamente no formato columnstore compactado, você precisa carregar mais de 6.1444 milhões de linhas (60 x 102.400) em uma única partição de uma tabela do SQL Data Warehouse.

## Etapa 5: Alocar recursos de computação adicionais

Frequentemente, a recriação de índices, especialmente em tabelas grandes, exige recursos adicionais. O SQL Data Warehouse tem um recurso de gerenciamento de carga de trabalho que pode alocar mais memória para um usuário. Para entender como reservar mais memória para recriações de índice, veja a seção de gerenciamento de carga de trabalho do artigo sobre [simultaneidade][].

## Etapa 6: Recriar o índice para melhorar a quantidade média de linhas por rowgroup

Para mesclar rowgroups compactados existentes e forçar os rowgroups delta no columnstore, é necessário recriar o índice. Geralmente, a recriação do índice inteiro envolve muitos dados, portanto, é melhor recriar uma ou mais partições. No SQL Data Warehouse, você poderá recriar o índice usando uma destas duas maneiras:

1. Use [ALTER INDEX][] para recriar uma partição.
2. Use [CTAS][] para recriar uma partição em uma nova tabela e, em seguida, usar a alternância de partição para mover a partição de volta para a tabela original.

Qual é a melhor opção? Para grandes volumes de dados, [CTAS][] é geralmente mais rápida do que [ALTER INDEX][]. Para volumes menores de dados, [ALTER INDEX][] é mais fácil de usar.

### Método nº 1: Usar ALTER INDEX para recriar pequenos volumes de dados offline

Estes exemplos mostram como recriar todo o índice columnstore e recriar uma partição única. Em tabelas grandes, é mais prático recriar apenas uma única partição. Esta é uma operação offline.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

Para saber mais, confira a seção ALTER INDEX REBUILD em [Columnstore Indexes Defragmentation](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1) (Desfragmentação de índices Columnstore) e o tópico da sintaxe [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx).

### Método nº 2: Usar CTAS para recriar e alternar a partição de grandes volumes de dados online

Este exemplo usa [CTAS][] e a alternância de partição para recriar uma partição de tabela.


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para saber mais detalhes sobre como recriar partições usando `CTAS`, confira [Particionamento de tabela][] e [Simultaneidade][].


## Próximas etapas

Para obter mais dicas de gerenciamento, acesse a visão geral de [gerenciamento][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Particionamento de tabela]: sql-data-warehouse-develop-table-partitions.md
[simultaneidade]: sql-data-warehouse-develop-concurrency.md
[gerenciamento]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0504_2016-->