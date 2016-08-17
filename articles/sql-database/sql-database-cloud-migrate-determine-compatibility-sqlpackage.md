<properties
   pageTitle="Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe | Microsoft Azure"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade com o Banco de Dados SQL, SqlPackage"
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
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de Atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo que você aprenderá a determinar se um banco de dados do SQL Server é compatível para migrar para o Banco de Dados SQL usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Como usar o SqlPackage.exe

1. Abra um prompt de comando e altere um diretório com a versão mais recente do sqlpackage.exe. Esse utilitário é fornecido com o Visual Studio e o SQL Server. Baixe a [versão mais recente do SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) e obtenha a versão mais recente do utilitário SqlPackage.
2. Execute o seguinte comando SqlPackage com os seguintes argumentos para o seu ambiente:

	'sqlpackage.exe /Action:Export /ssn:< nome\_do\_servidor > /sdn:< nome\_do\_banco\_de\_dados > /tf:< arquivo\_de\_destino > /p:TableData=< nome\_do\_esquema.nome\_da\_tabela > > < arquivo\_de\_saída > 2>&1'

	| Argumento | Descrição |
	|---|---|
	| < nome\_do\_servidor > | nome do servidor de origem |
	| < nome\_do\_banco\_de\_dados > | nome do banco de dados de origem |
	| < arquivo\_de\_destino > | nome do arquivo e um local para o arquivo BACPAC |
	| < nome\_do\_esquema.nome\_da\_tabela > | as tabelas cujos dados serão enviados para o arquivo de destino |
	| < arquivo\_de\_saída > | o nome do arquivo e o local do arquivo de saída com erros, se houver algum |

	O motivo da presença do argumento /p:TableName é que só queremos testar a compatibilidade de banco de dados para exportar para o Banco de Dados SQL do Azure V12, em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não dá suporte à extração de tabelas e, portanto, será necessário especificar uma pequena tabela única. O < arquivo\_de\_saída > conterá o relatório de erros. A cadeia de caracteres ">2>&1" redireciona a saída padrão e o erro padrão resultantes da execução do comando para o arquivo de saída especificado.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o arquivo de saída e examine os erros de compatibilidade, se houver.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração do banco de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Recursos adicionais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->