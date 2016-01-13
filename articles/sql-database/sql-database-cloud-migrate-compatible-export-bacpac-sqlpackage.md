<properties
   pageTitle="Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, exportar banco de dados, exportar arquivo BACPAC, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Este artigo mostra como exportar o banco de dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe - esse utilitário é fornecido com o Visual Studio e o SQL Server. Use a pesquisa em seu computador para localizar o caminho em seu ambiente.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argumento | Descrição |
	|---|---|
	| < server_name > | nome do servidor de origem |
	| < database_name > | nome do banco de dados de origem |
	| < target_file > | nome do arquivo e um local para o arquivo BACPAC |

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Próxima Etapa: importar para o Banco de Dados SQL a partir de um arquivo BACPAC

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->