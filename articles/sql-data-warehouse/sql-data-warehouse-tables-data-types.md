<properties
   pageTitle="Tipos de dados para as tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Introdução aos tipos de dados para as tabelas do Azure SQL Data Warehouse."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Tipos de dados para as tabelas no SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partition][]
- [Estatísticas][]
- [Temporário][]

O SQL Data Warehouse oferece suporte aos tipos comuns de dados usados. Abaixo está uma lista dos tipos de dados suportados pelo SQL Data Warehouse. Para obter detalhes adicionais sobre o suporte do tipo de dados, consulte [criar tabela][].

|**Tipos de dados com suporte**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## Práticas recomendadas do tipo de dados

 Ao definir seus tipos de coluna, usar o menor tipo de dados que oferecerá suporte a seus dados melhorará o desempenho da consulta. Isso é especialmente importante para as colunas CHAR e VARCHAR. Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25). Evite definir todas as colunas de caractere para um tamanho grande padrão. Além disso, defina as colunas como VARCHAR quando isso for tudo o que é necessário, em vez de usar [NVARCHAR][]. Use NVARCHAR(4000) ou VARCHAR(8000) sempre que possível, em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

## Limitação do Polybase

Se estiver usando o Polybase para carregar suas tabelas, defina as tabelas para que o tamanho máximo possível da linha, incluindo o comprimento total das colunas de tamanho variável, não exceda 32.767 bytes. Embora seja possível definir uma linha com dados de tamanho variável que podem exceder essa largura e carregar linhas com o BCP, você não poderá usar o Polybase para carregar esses dados. O suporte a Polybase para linhas amplas será adicionado em breve.

## Tipos de dados sem suporte

Se você estiver migrando o banco de dados de outra plataforma SQL, como o Banco de Dados SQL do Azure, conforme você migra, poderá encontrar alguns tipos de dados que não são suportados no SQL Data Warehouse. Abaixo, estão os tipos de dados sem suporte, bem como algumas alternativas que você pode usar no lugar dos tipos de dados sem suporte.

|Tipo de Dados|Solução alternativa|
|---|---|
|[geometria][]|[varbinary][]|
|[geografia][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[imagem][ntext,text,image]|[varbinary][]|
|[texto][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|Divida a coluna em várias colunas fortemente tipadas.|
|[tabela][]|Converta em tabelas temporárias.|
|[timestamp][]|Retrabalhe o código para usar [datetime2][] e a função`CURRENT_TIMESTAMP`. Somente as constantes são suportados como padrões, portanto, current\_timestamp não pode ser definida como uma restrição padrão. Se você precisar migrar os valores de versão da linha de uma coluna tipada com o carimbo de data/hora, use [BINARY][](8) ou [VARBINARY][BINARY](8) para os valores de versão da linha NOT NULL ou NULL.|
|[xml][]|[varchar][]|
|[tipos definidos pelo usuário][]|converta de volta nos tipos nativos onde for possível|
|valores padrão|os valores padrão dão suporte a literais e constantes somente. Não há suporte para expressões ou funções não determinísticas como `GETDATE()` ou `CURRENT_TIMESTAMP`.|

O SQL a seguir pode ser executado em seu banco de dados SQL atual para identificar as colunas que não são suportadas pelo SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## Próximas etapas

Para saber mais, consulte os artigos em [Visão Geral da Tabela][Overview], [Distribuindo uma Tabela][Distribute], [Indexando uma Tabela][Index], [Particionando uma Tabela][Partition], [Mantendo as Estatísticas da Tabela][Statistics] e [Tabelas Temporárias][Temporary]. Para saber mais sobre as práticas recomendadas, consulte [Práticas Recomendadas do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Práticas Recomendadas do SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[criar tabela]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometria]: https://msdn.microsoft.com/library/cc280487.aspx
[geografia]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tabela]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[tipos definidos pelo usuário]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->