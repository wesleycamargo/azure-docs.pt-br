---
title: Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage | Microsoft Docs
description: Banco de Dados SQL do Microsoft Azure, migração de banco de dados, exportar banco de dados, exportar arquivo BACPAC, sqlpackage
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> 
> 

Este artigo mostra como exportar o banco de dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário acompanha as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a versão mais recente do [SqlPackage](https://www.microsoft.com/pt-BR/download/details.aspx?id=53876) diretamente do Centro de Download da Microsoft.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe – esse utilitário é fornecido com o Visual Studio e o SQL Server. Use a pesquisa em seu computador para localizar o caminho em seu ambiente.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:
   
    'sqlpackage.exe /Action:Export /ssn:< nome\_do\_servidor > /sdn:< nome\_do\_banco\_de\_dados > /tf:< arquivo\_de\_destino >
   
   | Argumento | Descrição |
   | --- | --- |
   | < nome\_do\_servidor > |nome do servidor de origem |
   | < nome\_do\_banco\_de\_dados > |nome do banco de dados de origem |
   | < arquivo\_de\_destino > |nome do arquivo e um local para o arquivo BACPAC |
   
    ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Próximas etapas
* [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Importar um BACPAC para o Banco de Dados SQL do Azure usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Importar um BACPAC para o SqlPackage do Banco de Dados SQL do Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Importar um BACPAC para o Portal do Azure do Banco de Dados SQL do Azure](sql-database-import.md)
* [Importar um BACPAC para o PowerShell do Banco de Dados SQL do Azure](sql-database-import-powershell.md)

## Recursos adicionais
* [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
* [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
* [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->