<properties
	pageTitle="Conectar-se ao banco de dados SQL ‒ SQL Server Management Studio | Microsoft Azure"
	description="Saiba como se conectar a um banco de dados SQL no Azure usando o SSMS (SQL Server Management Studio). Em seguida, execute uma consulta de exemplo usando o Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="conectar-se ao banco de dados sql, sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="sstein;carlrab" />

# Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Este artigo mostra como conectar-se a um Banco de Dados SQL do Azure usando a versão mais recente do SQL Server Management Studio (SSMS) e executar uma consulta simples com instruções Transact-SQL.

[AZURE.INCLUDE [Entrar](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Instalação do SSMS](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [Conexão SSMS](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Para saber mais sobre regras de firewall, confira [Como definir as configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md).

## Executar consultas de exemplo

Depois que se conectar ao servidor lógico, você pode se conectar a um banco de dados e executar uma consulta de exemplo.

1. Em **Pesquisador de Objetos**, navegue até um banco de dados no servidor ao qual você tem permissão, como o exemplo de banco de dados **AdventureWorks**.
2. Clique com o botão direito do mouse no banco de dados e selecione **Nova consulta**.

	![Nova consulta. Conectar-se ao servidor de Banco de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Na janela de consulta, copie e cole o código a seguir.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Clique no botão **Executar**. A captura de tela a seguir mostra uma consulta bem-sucedida.

	![Êxito. Conectar-se ao servidor de Banco de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Próximas etapas

Você pode usar instruções T-SQL para criar e gerenciar bancos de dados no Azure da mesma forma que faz no SQL Server. Se você já sabe como usar o T-SQL com o SQL Server, consulte [Informações de Transact-SQL de Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) para obter um resumo das diferenças.

Se não tiver experiência com o T-SQL, consulte [Tutorial: Escrevendo instruções Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e a [Referência do Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx).

Para começar a criação de usuários de banco de dados e administradores de usuários de banco de dados, confira [Introdução à segurança do Banco de Dados SQL](sql-database-get-started-security.md)

<!---HONumber=AcomDC_0420_2016-->