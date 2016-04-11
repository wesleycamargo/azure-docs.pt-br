<properties
   pageTitle="Partições de tabela no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar partições de tabela no SQL Data Warehouse do Azure para desenvolvimento de soluções."
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
   ms.date="03/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Partições de tabela no SQL Data Warehouse

Migrar definições de partição do SQL Server para o SQL Data Warehouse:

- Remova funções de partição do SQL Server e esquemas porque isso é gerenciado por você ao criar a tabela.
- Defina as partições quando você criar a tabela. Basta especificar pontos de limite de partição e como deseja que o ponto de limite seja eficaz `RANGE RIGHT` ou `RANGE LEFT`.

OBSERVAÇÃO: Para saber mais sobre as partições no SQL Server, consulte [Tabelas e Índices Particionados](https://msdn.microsoft.com/library/ms190787.aspx).


### Dimensionamento da partição
O SQL DW oferece ao DBA várias opções de tipos de tabela: heap, CI (índice clusterizado) e CCI (índice de repositório de coluna clusterizado). Para cada um desses tipos de tabela, o DBA também pode particionar a tabela, que significa dividi-la em várias seções para melhorar o desempenho. No entanto, criar uma tabela com muitas partições pode, de fato, causar degradação do desempenho ou falhas de consulta sob algumas circunstâncias. Essas questões são especialmente verdadeiras para tabelas CCI. Para que o particionamento seja útil, é importante para um DBA entender quando usar o particionamento e o número de partições a serem criadas. Essas diretrizes se destinam a ajudar os DBAs a fazer as melhores escolhas para seus cenários.

Geralmente, as partições de tabela são úteis de duas maneiras importantes:

1. Usando a alternância de partição para truncar rapidamente uma seção de uma tabela. Um design usado frequentemente é de uma tabela de fatos para conter linhas somente por algum período finito predeterminado. Por exemplo, uma tabela de fatos de vendas pode conter dados somente dos últimos 36 meses. No final de cada mês, o mês de dados de vendas mais antigo é excluído da tabela. Para isso, basta excluir todas as linhas do mês mais antigo, mas excluir um grande volume de dados, linha por linha, pode demorar muito tempo. Para otimizar esse cenário, o SQL DW oferece suporte à permutação de partição, que permite que o conjunto inteiro de linhas em uma partição seja descartado em uma única operação rápida.   

2. O particionamento permite às consultas excluir facilmente o processamento de um grande conjunto de linhas (isto é, uma partição) se as consultas colocarem um predicado na coluna de particionamento. Por exemplo, se a tabela de fatos de vendas for particionada em 36 meses usando o campo de data das vendas, as consultas que forem filtradas pela data da venda podem pular o processamento de partições que não correspondam ao filtro. Na verdade, o particionamento usado dessa maneira é um índice grosseiro.

Ao criar índices de repositório de coluna clusterizado no SQL DW, um DBA precisa considerar um fator adicional: o número de linhas. As tabelas CCI podem atingir um alto grau de compactação e ajudam o SQL DW a acelerar o desempenho da consulta. Devido ao funcionamento interno da compactação no SQL DW, cada partição em uma tabela CCI precisa ter um número razoavelmente grande de linhas para que os dados sejam compactados. Além disso, o SQL DW espalha os dados por um grande número de distribuições, e cada distribuição ainda é dividida por partições. Para obter uma compactação e um desempenho ideais, é necessário um mínimo de 100.000 linhas por distribuição e partição. Usando o exemplo acima, se a tabela de fatos de vendas contiver 36 partições mensais, e uma vez que o SQL DW tem 60 distribuições, a tabela de fatos de vendas deverá conter 6 milhões de linhas por mês, ou 216 milhões de linhas quando todos os meses forem populados. Se uma tabela contiver significativamente menos linhas do que o mínimo recomendado, o DBA deverá considerar a criação de uma tabela com menos partições para que o número de linhas por distribuição seja maior.


Para dimensionar seu banco de dados SQL Server atual no nível de partição, use uma consulta como a mostrada abaixo:

```sql
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

O número total de distribuições é igual ao número de locais de armazenamento usado quando criamos uma tabela. É uma forma complexa de dizer que cada tabela é criada uma vez por distribuição.

Você também pode esperar aproximadamente quantas linhas e, portanto, o tamanho máximo, cada partição terá. A partição no data warehouse de origem será subdividida em cada distribuição.

Use o seguinte cálculo para orientá-lo ao determinar o tamanho da partição:

Tamanho da Partição MPP = Tamanho da Partição SMP/ número de distribuições

Você pode descobrir quantas distribuições tem de seu banco de dados do SQL Data Warehouse usando a seguinte consulta:

```sql
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Agora que você sabe qual é o tamanho máximo de cada partição no sistema de origem e qual o tamanho que está prevendo para SQLDW, é possível decidir sobre o limite da partição.

### Gerenciador de carga de trabalho
Uma informação final que é necessária considerar para a decisão da partição da tabela é o gerenciamento de carga de trabalho. No SQL Data Warehouse, a memória máxima alocada para cada distribuição durante a execução da consulta é regido por esse recurso. Consulte o seguinte artigo para obter mais detalhes sobre [gerenciamento de carga de trabalho]. De forma ideal, a partição será dimensionada com operações “inmemory”, como a recompilação do índice columnstore em mente. Uma recompilação do índice é uma operação de uso intensivo de memória. Portanto, você desejará garantir que a recompilação do índice da partição não fique sem memória. O aumento da quantidade de memória disponível para a sua consulta pode ser obtido ao alternar da função padrão para uma das outras funções disponíveis.

Informações sobre a alocação de memória por distribuição estão disponíveis consultando as exibições de gerenciamento dinâmico do administrador de recursos. Na realidade, a concessão de memória será menor do que apresentada abaixo. No entanto, isso fornece um nível de diretrizes que podem ser usados ao dimensionar suas partições para operações de gerenciamento de dados.

```sql
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

> [AZURE.NOTE] Tente evitar o dimensionamento das partições além da concessão de memória fornecida pela classe de recurso muito grande. Se as partições ultrapassarem este valor, você corre o risco de pressão de memória, que por sua vez, leva à menor compactação ideal.

## Alternância de partição
Para alternar as partições entre duas tabelas, você deve garantir que as partições alinhem em seus respectivos limites e que correspondam as definições de tabela. Como restrições de verificação não estão disponíveis para impor o intervalo de valores em uma tabela, a tabela de origem deve conter os mesmos limites de partição da tabela de destino. Se este não for o caso, a alternância de partição falhará, assim como os metadados da partição não serão sincronizados.

### Como dividir uma partição que contém dados
O método mais eficiente para dividir uma partição que já contém dados é usar um `CTAS` instrução. Se a tabela particionada é uma columnstore clusterizada, então a partição de tabela deve estar vazia antes que ela possa ser dividida.

Veja abaixo um exemplo de tabela columnstore particionada que contém uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Criando o objeto de estatística garantimos que os metadados de tabela sejam mais precisos. Se omitirmos a criação de estatísticas, o SQL Data Warehouse usará os valores padrão. Para obter detalhes sobre as estatísticas examine [estatísticas][].

Podemos consultar a contagem de linha utilizando a `sys.partitions` exibição do catálogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se tentarmos dividir essa tabela, obteremos um erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Nível 15, Estado 1, linha 44 da cláusula SPLIT da instrução ALTER PARTITION falhou porque a partição não está vazia. Somente partições vazias podem ser divididas quando existe um índice columnstore na tabela. Considere desabilitar o índice columnstore antes de emitir a instrução ALTER PARTITION, e então recriar o índice columnstore após a instrução ALTER PARTITION estar concluída.

No entanto, podemos usar `CTAS` para criar uma nova tabela para manter nossos dados.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

Como os limites de partição estão alinhados, uma alternância é permitida. Isso deixará a tabela de origem com uma partição vazia que podemos dividir posteriormente.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta a fazer é alinhar os dados para os novos limites de partição usando `CTAS` e alternar nossos dados de volta para a tabela principal

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Depois de ter concluído a movimentação dos dados, é uma boa ideia atualizar as estatísticas na tabela de destino para garantir que reflitam com precisão a nova distribuição dos dados em suas respectivas partições:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### Controle da origem do particionamento da tabela
Para evitar a definição da tabela de **rusting** em seu sistema de controle de origem, você talvez queira considerar a abordagem a seguir:

1. Criar a tabela como uma tabela particionada, mas sem valores de partição

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT` a tabela como parte do processo de implantação:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Com essa abordagem, o código no controle de origem permanece estático e são permitidos valores de limite de particionamento dinâmicos, evoluindo com o depósito ao longo do tempo.

>[AZURE.NOTE] A alternância de partição tem algumas diferenças em comparação ao SQL Server. Não deixe de ler [Migrar seu código][] para saber mais sobre esse assunto.


## Próximas etapas
Depois de migrar com êxito o esquema do seu banco de dados para o SQL Data Warehouse, você poderá passar para um dos artigos a seguir:

- [Migrar seus dados][]
- [Migrar seu código][]

<!--Image references-->

<!-- Article references -->
[Migrar seu código]: sql-data-warehouse-migrate-code.md
[Migrar seus dados]: sql-data-warehouse-migrate-data.md
[estatísticas]: sql-data-warehouse-develop-statistics.md
[gerenciamento de carga de trabalho]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=AcomDC_0330_2016-->