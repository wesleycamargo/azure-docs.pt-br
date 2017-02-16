---
title: "SAMW: Corrigir problemas de migração de compatibilidade do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Neste artigo, você aprenderá sobre as opções para detectar e corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Azure Migration Wizard antes da migração para o Banco de Dados SQL do Azure."
keywords: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: bba0893a-3247-406c-b155-acb4b3ef479c
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 006d2d5cd24bc42655bf0b3c0325531144743524


---
# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Use o SQL Azure Migration Wizard para corrigir problemas de compatibilidade de banco de dados SQL Server antes da migração para o Banco de dados SQL do Azure
> [!div class="op_single_selector"]
> * Usar [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

Neste artigo, você aprenderá a detectar e corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Azure Migration Wizard antes da migração para o Banco de Dados SQL do Azure.

## <a name="using-sql-azure-migration-wizard"></a>Assistente de Migração do SQL Azure
Use a ferramenta CodePlex do [Assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/) para gerar um script T-SQL de um banco de dados de origem incompatível. Esse script é transformado pelo assistente para torná-lo compatível com o banco de dados SQL. Em seguida, você conecta-se ao Banco de Dados SQL do Azure para executar o script. Essa ferramenta também analisa os arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado apenas com esquemas ou pode incluir dados no formato BCP. Há uma documentação adicional, incluindo instruções passo a passo, disponível no CodePlex em [Assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagrama de migração do SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> Nem todos os esquemas incompatíveis detectados pelo assistente podem ser corrigidos por suas transformações internas. O script incompatível que não puder ser incluído é apontado como erro, com comentários injetados no script gerado. Se vários erros forem detectados, use o Visual Studio ou o SQL Server Management Studio para depurar e corrigir cada erro que não puder ser corrigido pelo Assistente de Migração do SQL Server.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Recursos do Banco de Dados SQL](sql-database-features.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


