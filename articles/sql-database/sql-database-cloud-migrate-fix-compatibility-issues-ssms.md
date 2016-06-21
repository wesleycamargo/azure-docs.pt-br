<properties
   pageTitle="Corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL | Microsoft Azure"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
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

# Corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL

> [AZURE.SELECTOR]
- Usar [Assistente de Migração do SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Usuários avançados podem corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL do Azure.

## Como usar o SQL Server Management Studio.

Use o SQL Server Management Studio para corrigir problemas de compatibilidade usando vários comandos Transact-SQL, como **ALTER DATABASE**. Esse método é principalmente para usuários avançados que se sentem à vontade trabalhando com Transact-SQL em um banco de dados ao vivo. Caso contrário, é recomendável usar SSDT.



## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar um banco de dados SQL Server compatível para o Banco de Dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Saiba mais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->