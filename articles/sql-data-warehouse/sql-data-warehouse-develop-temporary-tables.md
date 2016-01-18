<properties
   pageTitle="Tabelas temporárias no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar tabelas temporárias no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Tabelas temporárias no SQL Data Warehouse
As tabelas temporárias existem no nível da sessão no SQL Data Warehouse. Elas são definidos como tabelas temporárias locais e ao contrário das tabelas do SQL Server podem ser acessadas de qualquer lugar dentro da sessão.

As tabelas temporárias são muito úteis durante o processamento de dados - especialmente durante a transformação onde os resultados intermediários são temporários.

Este artigo destaca alguns métodos específicos para aproveitar as tabelas temporárias para ajudá-lo a modularizar seu código.

## Modularizar o código

O fato de que as tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário pode ser explorado para ajudá-lo a modularizar o código do seu aplicativo.

Vamos fazer um exemplo funcional.

O procedimento armazenado a seguir gera a DDL necessária para atualizar as estatísticas em todas as colunas no banco de dados:

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
```

Observe que nada, na verdade, foi feito com esses dados. O procedimento simplesmente gerou a DDL e a armazenou em uma tabela temporária.

No entanto, é possível no SQL Data Warehouse usar essa tabela temporária fora do procedimento que a criou. Isso é diferente para o SQL Server. Na verdade, a tabela temporária pode ser usada **em qualquer lugar** na sessão.

Isso pode levar a um código mais modular e gerenciável.

```
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Em alguns casos, funções embutidas e de várias instruções também podem ser substituídas usando essa técnica.

> [AZURE.NOTE]Você também pode ampliar essa solução. Se você quiser atualizar uma única tabela por exemplo, tudo o que precisaria fazer é simplesmente filtrar a tabela #stats\_ddl

## Limitações da tabela temporária
O SQL Data Warehouse impõe algumas limitações ao implementar a tabelas temporárias.

As principais limitações são:

- Não há suporte para Tabelas Temporárias Globais
- Exibições não podem ser criadas em tabelas temporárias


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->