<properties
   pageTitle="Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade com o Banco de Dados SQL, SqlPackage"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Neste artigo que você aprenderá a determinar se um banco de dados do SQL Server é compatível para migrar para o Banco de Dados SQL usando o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Como usar o SqlPackage.exe

1. Abra um prompt de comando e altere um diretório com a versão mais recente do sqlpackage.exe. Esse utilitário é fornecido com o Visual Studio e o SQL Server. Baixe a [versão mais recente do SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) e obtenha a versão mais recente do utilitário SqlPackage.
2. Execute o seguinte comando SqlPackage com os seguintes argumentos para o seu ambiente:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Argumento | Descrição |
	|---|---|
	| < server_name > | nome do servidor de origem |
	| < database_name > | nome do banco de dados de origem |
	| < target_file > | nome do arquivo e um local para o arquivo BACPAC |
	| < schema_name.table_name > | as tabelas cujos dados serão enviados para o arquivo de destino |
	| < output_file > | o nome do arquivo e o local do arquivo de saída com erros, se houver algum |

	O motivo da presença do argumento /p:TableName é que só queremos testar a compatibilidade de banco de dados para exportar para o Banco de Dados SQL do Azure V12, em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não dá suporte à extração de tabelas e, portanto, será necessário especificar uma pequena tabela única. O < output_file > conterá o relatório de erros. A cadeia de caracteres ">2>&1" redireciona a saída padrão e o erro padrão resultantes da execução do comando para o arquivo de saída especificado.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o arquivo de saída e examine os erros de compatibilidade, se houver.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Próxima etapa: corrigir problemas de compatibilidade, se houver algum

[Corrigir problemas de compatibilidade do banco de dados](sql-database-cloud-migrate-fix-compatibility-issues.md), se houver algum.

<!---HONumber=AcomDC_0316_2016-->