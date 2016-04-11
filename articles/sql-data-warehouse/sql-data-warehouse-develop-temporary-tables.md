<properties
   pageTitle="Tabelas temporárias no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar tabelas temporárias no SQL Data Warehouse do Azure para desenvolvimento de soluções."
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
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Tabelas temporárias no SQL Data Warehouse
As tabelas temporárias são muito úteis durante o processamento de dados - especialmente durante a transformação onde os resultados intermediários são temporários. No SQL Data Warehouse, existem tabelas temporárias no nível de sessão. No entanto, elas ainda estão definidas como tabelas temporárias locais, mas diferentemente das tabelas do SQL Server, elas podem ser acessadas de qualquer lugar dentro da sessão.

Este artigo contém algumas diretrizes essenciais de como usar as tabelas temporárias e destaca os princípios das tabelas temporárias no nível de sessão. Usar essas informações pode ajudar você a modularizar seu código. A modularidade do código é importante para facilidade de manutenção e reutilização do código.

## Criando tabelas temporárias
Criar uma tabela temporária é bem simples e fácil. Tudo o que você precisa fazer é prefixar o nome da tabela com #, como no exemplo abaixo:

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

As tabelas temporárias também podem ser criadas usando `CTAS` com a mesma abordagem.

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
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

>[AZURE.NOTE] `CTAS` é um comando bastante potente e tem a vantagem de ser muito eficiente em seu uso de espaço de log de transações.


## Descartando tabelas temporárias

Para garantir que as declarações `CREATE TABLE` sejam bem-sucedidas, é importante garantir que a tabela ainda não exista na sessão. Isso pode ser tratado com uma simples verificação de pré-existência usando o padrão abaixo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] Para consistência de codificação, é recomendável usar esse padrão para tabelas e tabelas temporárias.

Também é uma boa prática usar `DROP TABLE` para remover tabelas temporárias quando você tiver finalizado o trabalho com elas no código.

```sql
DROP TABLE #stats_ddl
```

No desenvolvimento de procedimento armazenado, é bastante comum ver os comandos de remoção agrupados no fim de um procedimento para garantir que esses objetos sejam limpos.

## Modularizar o código

O fato de que as tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário pode ser explorado para ajudá-lo a modularizar o código do seu aplicativo.

Vamos fazer um exemplo funcional.

O procedimento armazenado abaixo reúne os exemplos mencionados acima. O código pode ser usado para gerar a DDL exigida na atualização das estatísticas em cada coluna no banco de dados:

```sql
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

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

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
GO
```

Neste estágio, nenhuma ação ocorreu na tabela. O procedimento simplesmente gerou a DDL necessária para atualizar as estatísticas e armazenou esse código em uma tabela temporária.

No entanto, observe também que o procedimento armazenado não inclui um comando `DROP TABLE` no fim. Entretanto, incluímos uma verificação de pré-existência no procedimento armazenado para tornar o código robusto e reproduzível; garantindo que nosso `CTAS` não falhará como consequência de um objeto duplicado existente na sessão.

Agora a parte interessante!

No SQL Data Warehouse, é possível usar a tabela temporária fora do procedimento que o criou. Isso é diferente para o SQL Server. Na verdade, a tabela temporária pode ser usada **em qualquer lugar** na sessão.

Isso pode levar a um código mais modular e gerenciável. Veja o exemplo abaixo:

```sql
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

O código resultante é muito mais compacto.

Em alguns casos, funções embutidas e de várias instruções também podem ser substituídas usando essa técnica.

> [AZURE.NOTE] Você também pode ampliar essa solução. Se você quiser atualizar uma única tabela por exemplo, tudo o que precisaria fazer é simplesmente filtrar a tabela #stats\_ddl

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

<!-----------HONumber=AcomDC_0330_2016-->