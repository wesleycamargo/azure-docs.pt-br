---
title: "SSMS: Corrigir problemas de migração de compatibilidade do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Neste artigo, você vai aprender a corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL do Azure."
keywords: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 841c4d644c6b096251e1ecefac17a8254b5ca712


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL
> [!div class="op_single_selector"]
> * Usar [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

Usuários avançados podem corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="using-sql-server-management-studio"></a>Como usar o SQL Server Management Studio.
Use o SQL Server Management Studio para corrigir problemas de compatibilidade usando vários comandos Transact-SQL, como **ALTER DATABASE**. Esse método é principalmente para usuários avançados que se sentem à vontade trabalhando com Transact-SQL em um banco de dados ao vivo. Caso contrário, é recomendável usar SSDT. 

## <a name="next-steps"></a>Próximas etapas
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Recursos do Banco de Dados SQL](sql-database-features.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


