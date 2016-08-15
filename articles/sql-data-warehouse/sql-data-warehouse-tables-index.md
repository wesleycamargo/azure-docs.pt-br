<properties
   pageTitle="Indexando tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Introdução à indexação de tabela no Azure SQL Data Warehouse."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Indexando tabelas no SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partition][]
- [Estatísticas][]
- [Temporário][]

O SQL Data Warehouse oferece várias opções de indexação, incluindo [índices columnstore clusterizados][], [índices clusterizados e índices não clusterizados][]. Além disso, ele oferece uma opção sem índice também conhecida como [heap][]. Este artigo aborda as vantagens de cada tipo de índice, bem como dicas para obter o melhor desempenho dos seus índices. Confira [sintaxe create table][] para obter mais detalhes sobre como criar uma tabela no SQL Data Warehouse.

## Índice columnstore clusterizado

Por padrão, o SQL Data Warehouse cria um índice columnstore clusterizado quando nenhuma opção de índice é especificada em uma tabela. As tabelas columnstore clusterizadas oferecem o nível mais alto de compactação de dados e o melhor desempenho de consulta geral. As tabelas columnstore clusterizadas geralmente superam as tabelas de índice clusterizado ou de heap e geralmente são a melhor opção para tabelas grandes. Por esses motivos, columnstore clusterizado é a melhor opção para começar quando você não tem certeza de como indexar sua tabela.

Para criar uma tabela columnstore clusterizada, basta especificar CLUSTERED COLUMNSTORE INDEX na cláusula WITH, ou não incluir a cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Há alguns cenários em que columnstore clusterizado pode não ser uma boa opção:

- As tabelas ColumnStore não dão suporte a índices não clusterizados secundários. Considere tabelas de índice clusterizado ou heap.
- Tabelas ColumnStore não dão suporte a varchar (max), nvarchar (max) e varbinary (max). Considere o heap ou índice clusterizado.
- As tabelas ColumnStore podem ser menos eficientes para dados transitórios. Considere a possibilidade de tabelas heap ou até mesmo temporárias.
- Tabelas pequenas com menos de cem milhões de linhas. Considere as tabelas de heap.

## Tabelas de heap

Quando você estiver descarregando temporariamente os dados no SQL Data Warehouse, poderá achar que usar uma tabela de heap tornará o processo geral mais rápido. Isso ocorre porque carregamentos de heaps são mais rápidos que as tabelas de índice e, em alguns casos, a leitura subsequente pode ser feita no cache. Se estiver carregando os dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido que carregar os dados em uma tabela columnstore clusterizado. Além disso, o carregamento de dados em uma [tabela temporária][Temporary] também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente.

Para tabelas de pesquisa pequenas, de menos de cem milhões de linhas, as tabelas de heap geralmente são adequadas. As tabelas columnstore do cluster começam a obter compactação ideal quando há mais de cem milhões de linhas.

Para criar uma tabela de heap, basta especificar HEAP na cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## Índices clusterizados e não clusterizados

Os índices clusterizados podem superar o desempenho de tabelas columnstore clusterizadas quando uma única linha precisa ser recuperada rapidamente. Para consultas em que é necessário pesquisar poucas vezes ou somente uma vez para desempenho com extrema velocidade, considere um índice de cluster ou um índice secundário não clusterizado. A desvantagem de usar um índice clusterizado é que apenas as consultas que usam um filtro altamente seletivo na coluna de índice clusterizado se beneficiarão. Para melhorar o filtro em outras colunas, um índice não clusterizado pode ser adicionado a outras colunas. No entanto, cada índice adicionado a uma tabela adicionará espaço e tempo de processamento às cargas.

Para criar uma tabela de índice clusterizado, basta especificar o CLUSTERED INDEX na cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não clusterizado em uma tabela, basta usar a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Um índice não clusterizado é criado por padrão quando CREATE INDEX é usado. Além disso, um índice não clusterizado é permitido somente em uma tabela de armazenamento de linha (HEAP ou CLUSTERED INDEX). Índices não clusterizados sobre um CLUSTERED COLUMNSTORE INDEX não são permitidos neste momento.


## Otimizando índices columnstore clusterizados

As tabelas columnstore clusterizadas são organizadas em dados em segmentos. Para segmentos de boa qualidade, é essencial obter um desempenho ideal em uma tabela columnstore. A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado. A qualidade do segmento é ideal quando há pelo menos cem mil linhas por grupo de linha compactado e ganho de desempenho conforme o número de linhas por grupo de linhas se aproxima das 1.048.576 linhas, que é a maior quantidade de linhas que um grupo de linhas pode conter.

A exibição abaixo pode ser criada e usada em seu sistema para calcular a média de linhas por grupo de linhas e identificar índices columnstore de cluster abaixo do ideal. A última coluna nessa exibição gera como instrução SQL que pode ser usada para recriar os índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,	COUNT(DISTINCT rg.[partition_number])					AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,	CEILING	((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
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
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que você criou o modo de exibição, execute essa consulta para identificar tabelas com grupos de linhas com menos de cem mil linhas. Claro, convém aumentar o limite de cem mil se você deseja aumentar a qualidade do segmento.

```sql
SELECT	*
FROM	[dbo].[vColumnstoreDensity]
WHERE	COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

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
| [Rebuild\_Index\_SQL] | SQL para recriar o índice columnstore para uma tabela |

## Causas de má qualidade de índice columnstore

Se você tiver identificado tabelas com segmentos de má qualidade, desejará identificar a causa raiz. A seguir estão algumas causas comuns de segmentos de má qualidade:

1. Pressão de memória quando o índice foi criado
2. Alto volume de operações DML
3. Operações de carregamento pequenas ou lentas
4. Número excessivo de partições

Esses fatores podem fazer com que um índice columnstore tenha menos que o ideal de um milhão de linhas por grupo de linhas. Eles também podem colocar as linhas no rowgroup delta, em vez do rowgroup compactado.

### Pressão de memória quando o índice foi criado

O número de linhas por grupo de linhas compactado está diretamente relacionado à largura da linha e à quantidade de memória disponível para processar o grupo de linhas. Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada. Portanto, a prática recomendada é fornecer à sessão que está gravando o acesso de tabelas de índice columnstore o máximo de memória possível. Como há uma compensação entre a memória e simultaneidade, a orientação sobre a alocação de memória correta depende dos dados em cada linha da tabela, da quantidade de DWUS que você alocou para o seu sistema e a quantidade de slots de simultaneidade que pode ser dada à sessão que está gravando dados em sua tabela. Como prática recomendada, sugerimos iniciar com xlargerc se você estiver usando DW300 ou menos, largerc se estiver usando DW400 DW600 e mediumrc se estiver usando DW1000 ou mais.

### Alto volume de operações DML

Um alto volume de operações DML pesadas que atualizam e excluem linhas pode causar ineficiência ao columnstore. Isso acontece principalmente quando a maioria das linhas em um rowgroup é modificada.

- A exclusão de uma linha de um rowgroup compactado somente marca logicamente a linha como excluída. A linha permanece no rowgroup compactado até que a partição ou a tabela seja recriada.
- A inserção de uma linha a adiciona a uma tabela interna da rowstore chamada de rowgroup delta. A linha inserida não é convertida para a columnstore, até que o rowgroup delta esteja cheio e marcado como fechado. Rowgroups são fechados quando atingem a capacidade máxima de 1.048.576 linhas.
- A atualização de uma linha no formato columnstore é processada como uma exclusão lógica e, em seguida, como uma inserção. A linha inserida pode ser armazenada no repositório delta.

As operações de atualização e inserção em lote que excedem o limite em massa de 102.400 linhas por distribuição alinhada em partição serão gravadas diretamente no formato columnstore. No entanto, supondo a ocorrência de uma distribuição uniforme, seria necessário modificar mais de 6.144 milhões de linhas em uma única operação para que isso ocorresse. Se o número de linhas de uma determinada distribuição alinhada em partição for menor do que 102.400, as linhas serão encaminhadas ao repositório delta e permanecerão lá até que uma quantidade suficiente de linhas seja inserida ou modificada, a fim de fechar o rowgroup ou recriar o índice.

### Operações de carregamento pequenas ou lentas

Às vezes, pequenas cargas que fluem para o SQL Data Warehouse também são chamadas de cargas lentas. Normalmente, elas representam um fluxo quase constante de dados que estão sendo incluídos pelo sistema. No entanto, como esse fluxo é quase contínuo, o volume de linhas não é grande. Frequentemente, os dados ficam consideravelmente abaixo do limite necessário para um carregamento direto no formato columnstore.

Nessas situações, é melhor levar os dados primeiro ao armazenamento de blobs do Azure e deixá-los se acumularem antes do carregamento. Essa técnica é conhecida normalmente como *micro envio em lote*.

### Número excessivo de partições

Outra coisa a considerar é o impacto de particionamento de suas tabelas columnstore clusterizadas. Antes do particionamento, o SQL Data Warehouse já divide seus dados em 60 bancos de dados. O particionamento divide ainda mais seus dados. Se particionar seus dados, você precisará considerar que **cada** partição deverá ter pelo menos um milhão de linhas para se beneficiar de um índice columnstore clusterizado. Se você dividir a tabela em 100 partições, a tabela deverá ter pelo menos seis bilhões de linhas para se beneficiar de um índice columnstore clusterizado (60 distribuições * 100 partições * um milhão de linhas). Se a tabela de cem partições não tiver seis bilhões de linhas, reduza o número de partições ou considere usar uma tabela de heap.

Quando as tabelas tiverem sido carregadas com alguns dados, siga as etapas abaixo para identificar e recriar tabelas com índices columnstore de cluster abaixo do ideal.

## Recriando índices para melhorar a qualidade de segmento

### Etapa 1: identificar ou criar o usuário que usa a classe de recurso correta

Uma maneira rápida de melhorar a qualidade do segmento imediatamente é recriar o índice. O SQL retornado pela exibição acima retornará uma instrução ALTER INDEX REBUILD, que pode ser usada para recriar os índices. Ao recriar os índices, não deixe de alocar memória suficiente para a sessão que recriará o índice. Para fazer isso, aumente a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela para o mínimo recomendado. A classe de recurso de usuário do proprietário do banco de dados não pode ser alterada. Se você não tiver criado um usuário no sistema, precisará fazer isso primeiro. Recomendamos, no mínimo, iniciar com xlargerc se você estiver usando DW300 ou menos, largerc se estiver usando DW400 DW600 e mediumrc se estiver usando DW1000 ou mais.

Abaixo está um exemplo de como alocar mais memória para um usuário aumentando sua classe de recurso. Mais informações sobre o recurso de classes e como criar um novo usuário podem ser encontradas no artigo [Gerenciamento de simultaneidade e carga de trabalho][Concurrency].

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### Etapa 2: recriar índices columnstore clusterizados com usuário de classe de recurso superior
Faça logon como o usuário da etapa 1 (por exemplo, LoadUser), que agora está usando uma classe de recurso maior, e execute as instruções ALTER INDEX. Verifique se esse usuário tem a permissão ALTER para as tabelas em que o índice está sendo recriado. Estes exemplos mostram como recriar todo o índice columnstore e como recriar uma partição única. Em tabelas grandes, é mais prático recriar índices, uma partição por vez.

Como alternativa, em vez de recompilar o índice, você pode copiar a tabela para uma nova tabela utilizando [CTAS][]. Qual é a melhor opção? Para grandes volumes de dados, [CTAS][] é geralmente mais rápida do que [ALTER INDEX][]. Para volumes menores de dados, [ALTER INDEX][] é mais fácil de usar e não exigirá a troca da tabela. Confira **Recriando índices com CTAS e alternância de partição** abaixo para obter mais detalhes sobre como recompilar índices com CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

A recriação de um índice no SQL Data Warehouse é uma operação offline. Para saber mais sobre a recompilação de índices, confira a seção ALTER INDEX REBUILD em [Columnstore Indexes Defragmentation][] \(Desfragmentação de Índices Columnstore) e o tópico da sintaxe [ALTER INDEX][].
 
### Etapa 3: verificar se melhorou a qualidade do segmento columnstore clusterizado
Execute novamente a consulta que identificou a tabela com segmentos de má qualidade e verifique se a qualidade melhorou. Se a qualidade do segmento não melhorou, é possível que as linhas da tabela sejam muito amplas. Considere usar uma classe de recurso maior ou mais DWU durante a recriação de índices. Se for necessário ter mais memória,

 
## Recriando índices com CTAS e alternância de partição

Este exemplo usa [CTAS][] e a alternância de partição para recompilar uma partição de tabela.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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

-- Step 2: Create a SWITCH out table
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

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obter mais detalhes sobre como recriar partições usando `CTAS`, confira o artigo [Partição][].

## Próximas etapas

Para saber mais, confira os artigos em [Visão Geral da Tabela][Overview], [Tipos de Dados de Tabela][Data Types], [Distribuindo uma Tabela][Distribute], [Particionando uma Tabela][Partition], [Mantendo Estatísticas de Tabela][Statistics] e [Tabelas Temporárias][Temporary]. Para saber mais sobre as práticas recomendadas, confira [Práticas recomendadas do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Práticas recomendadas do SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[índices clusterizados e índices não clusterizados]: https://msdn.microsoft.com/library/ms190457.aspx
[sintaxe create table]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[índices columnstore clusterizados]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0803_2016-->