---
title: Migrar seu esquema para o SQL Data Warehouse | Microsoft Docs
description: "Dicas para migrar seu esquema para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrar seu esquema para o SQL Data Warehouse
Os resumos a seguir ajudam você a entender as diferenças entre o SQL Server e o SQL Data Warehouse para ajudar na migração do banco de dados.

## <a name="table-migration"></a>Migração da Tabela
Ao migrar suas tabelas, você desejará se familiarizar com os recursos de tabela das tabelas do SQL Data Warehouse.  A [visão geral da tabela][visão geral da tabela] é um ótimo lugar para começar.  Este artigo apresenta as mais importantes considerações ao criar uma tabela, como as estatísticas, distribuição, particionamento e indexação da tabela.  Também aborda alguns [recursos sem suporte da tabela][recursos sem suporte da tabela] e soluções alternativas.

O SQL Data Warehouse dá suporte a tipos comuns de dados corporativos.  Consulte o artigo [tipos de dados][tipos de dados] para obter uma lista de [tipos de dados com e sem suporte][tipos de dados com e sem suporte] e com suporte.  O artigo [tipos de dados][tipos de dados] também contém uma consulta para identificar [tipos de dados com e sem suporte][tipos de dados com e sem suporte].  Ao converter os tipos de dados, consulte as [práticas recomendadas do tipo de dados][práticas recomendadas do tipo de dados].

## <a name="next-steps"></a>Próximas etapas
Depois de migrar com êxito o esquema do seu banco de dados para o SQL Data Warehouse, passe para um dos artigos a seguir:

* [Migrar seus dados][Migrar seus dados]
* [Migrar seu código][Migrar seu código]

Para saber mais sobre as práticas recomendadas do SQL Data Warehouse, confira o artigo sobre as [práticas recomendadas][práticas recomendadas].

<!--Image references-->

<!--Article references-->
[Migrar seu código]: ./sql-data-warehouse-migrate-code.md
[Migrar seus dados]: ./sql-data-warehouse-migrate-data.md
[práticas recomendadas]: ./sql-data-warehouse-best-practices.md
[visão geral da tabela]: ./sql-data-warehouse-tables-overview.md
[recursos sem suporte da tabela]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[tipos de dados com e sem suporte]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[práticas recomendadas do tipo de dados]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


