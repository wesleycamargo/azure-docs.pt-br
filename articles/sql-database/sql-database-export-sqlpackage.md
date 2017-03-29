---
title: 'SqlPackage: Exportar um banco de dados SQL Server para um arquivo BACPAC (Azure) | Microsoft Docs'
description: "Este artigo mostra como exportar o banco de dados SQL Server para um arquivo BACPAC usando o utilitário de linha de comando SqlPackage."
keywords: "Banco de Dados SQL do Microsoft Azure, migração de banco de dados, exportar banco de dados, exportar arquivo BACPAC, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698
ms.lasthandoff: 02/11/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportar um banco de dados SQL do Azure ou um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage

Este artigo mostra como exportar o banco de dados SQL do Azure ou um banco de dados do SQL Server para um arquivo BACPAC usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Para obter uma visão geral de como exportar para um arquivo BACPAC, confira [Exportar para um BACPAC](sql-database-export.md).

> [!NOTE]
> Você também pode exportar o arquivo de banco de dados SQL do Azure para um arquivo BACPAC usando o [Portal do Azure](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) ou [PowerShell](sql-database-export-powershell.md).
>

## <a name="sqlpackage-utility"></a>Utilitário SQLPackage

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe – esse utilitário é fornecido com o Visual Studio e o SQL Server. Use a pesquisa em seu computador para localizar o caminho em seu ambiente.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:
   
```    sqlpackage.exe /Action:Export /ssn:< nome_do_servidor > /sdn:< nome_do_banco_de_dados > /tf:< arquivo_de_destino >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](scripts/sql-database-import-from-bacpac-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)



