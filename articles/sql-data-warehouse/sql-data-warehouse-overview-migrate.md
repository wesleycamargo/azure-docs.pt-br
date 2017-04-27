---
title: "Migrar sua solução para o SQL Data Warehouse | Microsoft Docs"
description: "Orientação de migração para levar sua solução até a plataforma SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
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
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migrar sua solução para o SQL Data Warehouse
O SQL Data Warehouse é um sistema de banco de dados distribuído que pode ser dimensionado para atender às suas necessidades. Para manter o desempenho e a escala, nem todos os recursos do SQL Server são implementados no SQL Data Warehouse. Os seguintes tópicos de migração abordam alguns dos principais fatores para a migração de sua solução para o SQL Data Warehouse. O design de data warehouses, de acordo com a escala, apresenta padrões de design diferentes. Portanto, as abordagens tradicionais nem sempre são as melhores. Provavelmente, você perceberá que é melhor adaptar sua solução para garantir o aproveitamento total da plataforma distribuída fornecida pelo SQL Data Warehouse.

Também é importante lembrar de que o SQL Data Warehouse é uma plataforma baseada no Microsoft Azure. Portanto, parte de sua migração também pode incluir a transferência de dados para a nuvem. A transferência de dados é um assunto por si só e deve ser considerado com cuidado; especialmente à medida que os volumes aumentam. A transferência de dados e o carregamento de dados são tópicos distintos.

## <a name="migration-guidance"></a>Guia de migração
Antes de iniciar sua migração, leia estes artigos para entender algumas das diferenças entre os produtos e os conceitos fundamentais.

* [Migrar seu esquema][Migrate your schema]
* [Migrar seus dados][Migrate your data]
* [Migrar seu código][Migrate your code]

## <a name="next-steps"></a>Próximas etapas
A CAT (Equipe Consultiva para Clientes) também tem algumas diretrizes ótimas do SQL Data Warehouse, que publica por meio de blogs.  Dê uma olhada no artigo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migrando dados para o SQL Data Warehouse do Azure na prática) para obter diretrizes adicionais sobre a migração.

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

