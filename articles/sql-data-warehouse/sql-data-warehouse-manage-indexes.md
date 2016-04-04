<properties
   pageTitle="Gerenciando índices | Microsoft Azure"
   description="Diretrizes para ajudar os usuários a gerenciar seus índices"
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

# Gerenciando índices
Este artigo demonstra algumas técnicas básicas para gerenciar seus índices.

## Otimizando as recompilações de índice
É possível otimizar a recompilação de índice de duas maneiras:

1. Particionar a tabela e executar recompilações de índice no nível de partição
2. Usar o [CTAS][] para recriar a partição dos dados em uma nova tabela e alternar a partição na nova tabela

## Recompilações de índice particionado

Veja abaixo um exemplo de como recompilar uma única partição:

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD é melhor usado para volumes menores de dados – especialmente em índices columnstore. Rowgroups abertos, fechados e compactados são incluídos na recompilação. No entanto, caso a partição seja muito grande, você verá que `CTAS` é a operação mais eficiente. Veja abaixo um exemplo de uma recompilação de índice completo

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Consulte o artigo [ALTER INDEX][] para obter mais detalhes sobre essa sintaxe.

## Usando o CTAS para recompilar uma partição

Veja abaixo um exemplo de como recompilar uma partição usando CTAS:

```
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

## Próximas etapas
Consulte o artigo sobre [particionamento de tabela][] para obter diretrizes sobre como criar e dimensionar partições. O artigo também contém um exemplo para ajudar a identificar os limites de partição.

Para obter mais dicas de gerenciamento, vá para a visão geral de [gerenciamento][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[particionamento de tabela]: sql-data-warehouse-develop-table-partitions.md
[gerenciamento]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/pt-BR/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->