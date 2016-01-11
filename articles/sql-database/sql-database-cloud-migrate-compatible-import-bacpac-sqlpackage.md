<properties
   pageTitle="Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage"
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

# Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar o Banco de Dados SQL de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.

> [AZURE.NOTE]As etapas a seguir supõem que você já configurou um servidor de Banco de Dados SQL, tem as informações de conexão disponíveis e verificou se o banco de dados de origem é compatível.

## Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage

Use as etapas abaixo para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou um banco de dados SQL do Azure) de um arquivo BACPAC.

> [AZURE.NOTE]As etapas abaixo supõem que você já tenha provisionado um servidor do Banco de Dados SQL do Azure e tenha as informações sobre a conexão a seu alcance.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe - esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Argumento | Descrição |
	|---|---|
	| < server_name > | nome do servidor de destino |
	| < database_name > | nome do banco de dados de destino |
	| < user_name > | o nome de usuário no servidor de destino |
	| < password > | a senha do usuário |
	| < source_file > | o nome e o local do arquivo BACPAC que está sendo importado |

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_1223_2015-->