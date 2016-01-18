<properties
   pageTitle="Decisões de design e técnicas de codificação para o desenvolvimento do SQL Data Warehouse | Microsoft Azure"
   description="Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse."
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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Decisões de design e técnicas de codificação para o SQL Data Warehouse

Dê uma olhada nesses artigos sobre desenvolvimento para entender melhor as principais decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.

## Principais decisões de design
Os artigos a seguir destacam alguns dos principais conceitos e as decisões de design que você precisará entender para desenvolver seu data warehouse distribuído usando o SQL Data Warehouse:

- [conexões][]
- [simultaneidade][]
- [transações][]
- [esquemas definidos pelo usuário][]
- [design de tabela][]
- [chaves de distribuição de hash][]
- [partições de tabela][]
- [CTAS][]
- [estatísticas][]

## Recomendações para o desenvolvimento e técnicas de codificação
Esses artigos realçam as técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de seu SQL Data Warehouse:

- [procedimentos armazenados][]
- [rótulos][]
- [modos de exibição][]
- [tabelas temporárias][]
- [SQL dinâmico][]
- [looping][]
- [renomear objetos][]
- [dinamização de dados][]
- [agrupar por opções][]
- [atribuição de variável][]

## Próximas etapas
Após a leitura dos artigos de desenvolvimento, confira a página [Referência a Transact-SQL][] para obter mais detalhes sobre a sintaxe com suporte para o SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[simultaneidade]: sql-data-warehouse-develop-concurrency.md
[conexões]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[SQL dinâmico]: sql-data-warehouse-develop-dynamic-sql.md
[agrupar por opções]: sql-data-warehouse-develop-group-by-options.md
[chaves de distribuição de hash]: sql-data-warehouse-develop-hash-distribution-key.md
[rótulos]: sql-data-warehouse-develop-label.md
[looping]: sql-data-warehouse-develop-loops.md
[dinamização de dados]: sql-data-warehouse-develop-pivot-unpivot.md
[renomear objetos]: sql-data-warehouse-develop-rename.md
[estatísticas]: sql-data-warehouse-develop-statistics.md
[procedimentos armazenados]: sql-data-warehouse-develop-stored-procedures.md
[design de tabela]: sql-data-warehouse-develop-table-design.md
[partições de tabela]: sql-data-warehouse-develop-table-partitions.md
[tabelas temporárias]: sql-data-warehouse-develop-temporary-tables.md
[transações]: sql-data-warehouse-develop-transactions.md
[esquemas definidos pelo usuário]: sql-data-warehouse-develop-user-defined-schemas.md
[atribuição de variável]: sql-data-warehouse-develop-variable-assignment.md
[modos de exibição]: sql-data-warehouse-develop-views.md

[Referência a Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->