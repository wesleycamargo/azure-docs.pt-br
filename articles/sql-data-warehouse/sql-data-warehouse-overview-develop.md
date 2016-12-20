---
title: "Decisões de design e técnicas de codificação para o desenvolvimento do SQL Data Warehouse | Microsoft Docs"
description: "Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de design e técnicas de codificação para o SQL Data Warehouse
Dê uma olhada nesses artigos sobre desenvolvimento para entender melhor as principais decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam alguns dos principais conceitos e as decisões de design que você precisará entender para desenvolver seu data warehouse distribuído usando o SQL Data Warehouse:

* [conexões][conexões]
* [simultaneidade][simultaneidade]
* [transações][transações]
* [esquemas definidos pelo usuário][esquemas definidos pelo usuário]
* [distribuição da tabela][distribuição da tabela]
* [índices da tabela][índices da tabela]
* [partições de tabela][partições de tabela]
* [CTAS][CTAS]
* [estatísticas][estatísticas]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Esses artigos realçam as técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de seu SQL Data Warehouse:

* [procedimentos armazenados][procedimentos armazenados]
* [rótulos][rótulos]
* [modos de exibição][modos de exibição]
* [tabelas temporárias][tabelas temporárias]
* [SQL dinâmico][SQL dinâmico]
* [looping][looping]
* [agrupar por opções][agrupar por opções]
* [atribuição de variável][atribuição de variável]

## <a name="next-steps"></a>Próximas etapas
Após a leitura dos artigos de desenvolvimento, confira a página [Referência a Transact-SQL][Referência a Transact-SQL] para mais detalhes sobre a sintaxe com suporte para SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[simultaneidade]: ./sql-data-warehouse-develop-concurrency.md
[conexões]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinâmico]: ./sql-data-warehouse-develop-dynamic-sql.md
[agrupar por opções]: ./sql-data-warehouse-develop-group-by-options.md
[rótulos]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[estatísticas]: ./sql-data-warehouse-tables-statistics.md
[procedimentos armazenados]: ./sql-data-warehouse-develop-stored-procedures.md
[distribuição da tabela]: ./sql-data-warehouse-tables-distribute.md
[índices da tabela]: ./sql-data-warehouse-tables-index.md
[partições de tabela]: ./sql-data-warehouse-tables-partition.md
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md
[transações]: ./sql-data-warehouse-develop-transactions.md
[esquemas definidos pelo usuário]: ./sql-data-warehouse-develop-user-defined-schemas.md
[atribuição de variável]: ./sql-data-warehouse-develop-variable-assignment.md
[modos de exibição]: ./sql-data-warehouse-develop-views.md
[Referência a Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renomeando objetos]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


