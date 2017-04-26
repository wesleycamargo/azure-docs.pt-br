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
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrar seu esquema para o SQL Data Warehouse
Os resumos a seguir ajudam você a entender as diferenças entre o SQL Server e o SQL Data Warehouse para ajudar na migração do banco de dados.

## <a name="table-migration"></a>Migração da Tabela
Ao migrar suas tabelas, você desejará se familiarizar com os recursos de tabela das tabelas do SQL Data Warehouse.  A [visão geral da tabela][table overview] é um ótimo lugar para começar.  Este artigo apresenta as mais importantes considerações ao criar uma tabela, como as estatísticas, distribuição, particionamento e indexação da tabela.  Ele também aborda alguns [recursos sem suporte da tabela][unsupported table features] e soluções alternativas.

O SQL Data Warehouse dá suporte a tipos comuns de dados corporativos.  Consulte o artigo [tipos de dados][data types] para obter uma lista de [tipos de dados sem suporte][unsupported data types] e com suporte.  O artigo [tipos de dados][data types] também contém uma consulta para identificar [tipos de dados sem suporte][unsupported data types].  Ao converter os tipos de dados, certifique-se de examinar as [práticas recomendadas do tipo de dados][data type best practices].

## <a name="next-steps"></a>Próximas etapas
Depois de migrar com êxito o esquema do seu banco de dados para o SQL Data Warehouse, passe para um dos artigos a seguir:

* [Migrar seus dados][Migrate your data]
* [Migrar seu código][Migrate your code]

Para saber mais sobre as práticas recomendadas do SQL Data Warehouse, confira o artigo sobre as [práticas recomendadas][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

