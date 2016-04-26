<properties
	pageTitle="Tutorial do Banco de Dados SQL: Criar um banco de dados SQL | Microsoft Azure"
	description="Saiba como configurar um servidor lógico do Banco de Dados SQL, uma regra de firewall de servidor, um banco de dados SQL, dados de exemplo, conectar-se com ferramentas de cliente, configurar usuários e regras de firewall de banco de dados."
	keywords="tutorial do banco de dados sql, criar um banco de dados sql"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="carlrab"/>

# Tutorial do Banco de Dados SQL: criar um banco de dados SQL em alguns minutos usando o Portal do Azure

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Neste tutorial, você aprenderá a usar o portal do Azure para:

- Criar um servidor lógico do Banco de Dados SQL para hospedar bancos de dados SQL
- Crie um banco de dados SQL sem dados, com dados de exemplo ou com dados de um backup de banco de dados SQL.
- Crie uma regra de firewall de nível de servidor para um único endereço IP ou para um intervalo de endereços IP.

Use estes links para executar as mesmas tarefas usando [C#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Logon](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Criar um servidor lógico do Banco de Dados SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Próximas etapas
Agora que você concluiu este tutorial do Banco de Dados SQL e que criou um banco de dados com alguns dados de exemplo, você está pronto para explorar o uso de suas ferramentas favoritas.

- Se você está familiarizado com o Transact-SQL e o SQL Server Management Studio, saiba como [Conectar e consultar um banco de dados SQL com o SSMS](sql-database-connect-query-ssms.md).

- Se você está familiarizado com o Excel, saiba como [Conectar-se ao banco de dados SQL com o Excel](sql-database-connect-excel.md).

- Se você estiver pronto para começar a codificação, consulte [Conectar e consultar o banco de dados SQL com o C#](sql-database-connect-query.md) e [Usando o banco de dados SQL por meio do .NET (C#)](sql-database-develop-dotnet-simple.md). Veja os [Exemplos de código de início rápido para o banco de dados SQL](sql-database-develop-quick-start-client-code-samples.md) para obter exemplos do Node.js, Python, Ruby, Java, PHP e C++ e instruções adicionais para o C#.

- Se deseja mover seus bancos de dados SQL Server local para o Azure, consulte [Migrando um banco de dados para o Banco de dados SQL do Azure](sql-database-cloud-migrate.md) para obter mais informações.


## Recursos adicionais

[Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0420_2016-->