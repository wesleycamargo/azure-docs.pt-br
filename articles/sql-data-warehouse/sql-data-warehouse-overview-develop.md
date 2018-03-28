---
title: Recursos para o desenvolvimento de um data warehouse no Azure | Microsoft Docs
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 03/15/2018
ms.author: jrj;barbkess
ms.openlocfilehash: 329217faaf865052b79a1d44200cc3c788702046
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de design e técnicas de codificação para o SQL Data Warehouse
Leia estes artigos sobre desenvolvimento para reconhecer melhor as principais decisões de design, recomendações e técnicas de codificação para SQL Data Warehouse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam conceitos e decisões de design para desenvolver um data warehouse distribuído utilizando o SQL Data Warehouse:

* [conexões][connections]
* [simultaneidade][concurrency]
* [transações][transactions]
* [esquemas definidos pelo usuário][user-defined schemas]
* [distribuição de tabelas][table distribution]
* [índices da tabela][table indexes]
* [partições da tabela][table partitions]
* [CTAS][CTAS]
* [estatísticas][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Esses artigos destacam recomendações, dicas e técnicas de codificação específicas para o desenvolvimento do SQL Data Warehouse:

* [procedimentos armazenados][stored procedures]
* [rótulos][labels]
* [modos de exibição][views]
* [tabelas temporárias][temporary tables]
* [SQL dinâmico][dynamic SQL]
* [looping][looping]
* [agrupar por opções][group by options]
* [atribuição de variável][variable assignment]

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte a página de [referência Transact-SQL][Transact-SQL reference] para SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
