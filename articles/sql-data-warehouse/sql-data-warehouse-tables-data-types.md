---
title: Tipos de dados para as tabelas no SQL Data Warehouse | Microsoft Docs
description: "Introdução aos tipos de dados para as tabelas do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.contentlocale: pt-br
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipos de dados para as tabelas no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Tipos de Dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> 
> 

O SQL Data Warehouse oferece suporte aos tipos comuns de dados usados.  Abaixo está uma lista dos tipos de dados suportados pelo SQL Data Warehouse.  Para obter mais detalhes sobre o suporte do tipo de dados, consulte [create table][create table].

| **Tipos de dados com suporte** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>Práticas recomendadas do tipo de dados
 Ao definir seus tipos de coluna, usar o menor tipo de dados que oferecerá suporte a seus dados melhorará o desempenho da consulta. Isso é especialmente importante para as colunas CHAR e VARCHAR. Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25). Evite definir todas as colunas de caractere para um tamanho grande padrão. Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar [NVARCHAR][NVARCHAR].  Use NVARCHAR(4000) ou VARCHAR(8000) sempre que possível, em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

## <a name="polybase-limitation"></a>Limitação do Polybase
Se estiver usando o Polybase para carregar as tabelas, verifique se o tamanho dos dados não excede 1 MB.  Embora seja possível definir uma linha com dados de tamanho variável que podem exceder essa largura e carregar linhas com o BCP, você não poderá usar o Polybase para carregar esses dados.  

## <a name="unsupported-data-types"></a>Tipos de dados sem suporte
Se você estiver migrando o banco de dados de outra plataforma SQL, como o Banco de Dados SQL do Azure, conforme você migra, poderá encontrar alguns tipos de dados que não são suportados no SQL Data Warehouse.  Abaixo, estão os tipos de dados sem suporte, bem como algumas alternativas que você pode usar no lugar dos tipos de dados sem suporte.

| Tipo de Dados | Solução alternativa |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Divida a coluna em várias colunas fortemente tipadas. |
| [table][table] |Converta em tabelas temporárias. |
| [timestamp][timestamp] |Retrabalhe o código para usar [datetime2][datetime2] e a função `CURRENT_TIMESTAMP`.  Somente as constantes são suportados como padrões, portanto, current_timestamp não pode ser definida como uma restrição padrão. Se precisar migrar os valores de versão de linha de uma coluna tipada com o carimbo de data/hora, use [BINARY][BINARY](8) ou [VARBINARY][BINARY](8) para os valores de versão de linha NOT NULL ou NULL. |
| [xml][xml] |[varchar][varchar] |
| [tipos definidos pelo usuário][user defined types] |converta de volta nos tipos nativos onde for possível |
| valores padrão |os valores padrão dão suporte a literais e constantes somente.  Não há suporte para expressões ou funções não determinísticas como `GETDATE()` ou `CURRENT_TIMESTAMP`. |

O SQL a seguir pode ser executado em seu banco de dados SQL atual para identificar as colunas que não são suportadas pelo SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os artigos sobre [Visão geral de tabelas][Overview], [Distribuindo uma tabela][Distribute], [Indexando uma tabela][Index], [Particionando uma tabela][Partition], [Mantendo estatísticas da tabela][Statistics] e [Tabelas temporárias][Temporary].  Para saber mais sobre as práticas recomendadas, consulte [Práticas Recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
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
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
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
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx

