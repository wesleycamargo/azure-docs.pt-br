<properties
   pageTitle="Migrar sua solução para o SQL Data Warehouse | Microsoft Azure"
   description="Orientação de migração para levar sua solução até a plataforma SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/17/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Migrar sua solução para o SQL Data Warehouse

O SQL Data Warehouse é um sistema de banco de dados distribuído que pode ser dimensionado para atender às suas necessidades. Para manter o desempenho e a escala, nem todos os recursos do SQL Server são implementados no SQL Data Warehouse. Os seguintes tópicos de migração abordam alguns dos principais fatores para a migração de sua solução para o SQL Data Warehouse. O design de data warehouses, de acordo com a escala, apresenta padrões de design diferentes. Portanto, as abordagens tradicionais nem sempre são as melhores. Provavelmente, você perceberá que é melhor adaptar sua solução para garantir o aproveitamento total da plataforma distribuída fornecida pelo SQL Data Warehouse.

Também é importante lembrar de que o SQL Data Warehouse é uma plataforma baseada no Microsoft Azure. Portanto, parte de sua migração também pode incluir a transferência de dados para a nuvem. A transferência de dados é um assunto por si só e deve ser considerado com cuidado; especialmente à medida que os volumes aumentam. A transferência de dados e o carregamento de dados são tópicos distintos.

## Guia de migração
Antes de iniciar sua migração, leia estes artigos para entender algumas das diferenças entre os produtos e os conceitos fundamentais.

- [Migrar seu esquema][]
- [Migrar seus dados][]
- [Migrar seu código][]

## Próximas etapas
Para obter outras dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento][].

Você também pode exibir a [referência de Transact-SQL][] para saber mais.

Por fim, confira a [visão geral sobre carregamento][]. Esse artigo aborda diversas opções de carregamento de dados e fornece orientação detalhada.

<!--Image references-->

<!--Article references-->
[Migrar seu esquema]: sql-data-warehouse-migrate-schema.md
[Migrar seus dados]: sql-data-warehouse-migrate-data.md
[Migrar seu código]: sql-data-warehouse-migrate-code.md

[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md
[visão geral sobre carregamento]: sql-data-warehouse-overview-load.md
[referência de Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->