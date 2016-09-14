<properties
   pageTitle="Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar o Banco de Dados SQL de um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.

> [AZURE.NOTE] As etapas a seguir supõem que você já configurou um servidor de Banco de Dados SQL, tem as informações de conexão disponíveis e verificou se o banco de dados de origem é compatível.

## Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage

Use as etapas a seguir para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou um banco de dados SQL do Azure) de um arquivo BACPAC.

> [AZURE.NOTE] As etapas a seguir supõem que você já tenha provisionado um servidor do Banco de Dados SQL do Azure e tenha as informações sobre a conexão a seu alcance.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe – esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:

	`sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

	| Argumento | Descrição |
	|---|---|
	| < nome\_do\_servidor > | nome do servidor de destino |
	| < nome\_do\_banco\_de\_dados > | nome do banco de dados de destino |
	| < nome\_de\_usuário > | o nome de usuário no servidor de destino |
	| < senha > | a senha do usuário |
	| < arquivo\_de\_origem > | o nome e o local do arquivo BACPAC que está sendo importado |

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Recursos adicionais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0831_2016-->