<properties
	pageTitle="Tutorial do Banco de Dados SQL: Criar um banco de dados SQL | Microsoft Azure"
	description="Saiba como configurar um servidor lógico do Banco de Dados SQL, regra de firewall do servidor, banco de dados SQL e dados de exemplo. Além disso, saiba como conectar com as ferramentas do cliente, configurar usuários e configurar uma regra de firewall do banco de dados."
	keywords="tutorial do banco de dados SQL, criar um banco de dados SQL"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# Tutorial do Banco de Dados SQL: criar um banco de dados SQL em minutos usando o portal do Azure

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Neste tutorial, você aprenderá a usar o portal do Azure para:

- Crie um servidor lógico do Banco de Dados SQL do Azure para hospedar os bancos de dados SQL.
- Crie um banco de dados SQL sem dados, com dados de exemplo ou com dados de um backup do banco de dados SQL.
- Crie uma regra de firewall de nível de servidor para um único endereço IP ou para um intervalo de endereços IP.

Você pode executar as mesmas tarefas usando o [C#](sql-database-get-started-csharp.md) ou o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Logon](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [Criar um servidor lógico do Banco de Dados SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Próximas etapas
Agora que você concluiu este tutorial do Banco de Dados SQL e criou um banco de dados com alguns dados de exemplo, está pronto para explorar usando suas ferramentas favoritas.

- Se você estiver familiarizado com o Transact-SQL e o SQL Server Management Studio (SSMS), saiba como [Conectar e consultar um banco de dados SQL com o SSMS](sql-database-connect-query-ssms.md).

- Se você conhece o Excel, saiba como [Conectar um banco de dados SQL no Azure com o Excel](sql-database-connect-excel.md).

- Se você estiver pronto para começar a codificar, escolha a linguagem de programação em [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md).

- Se você quiser mover seus bancos de dados SQL Server locais para o Azure, consulte [Migrando um banco de dados para o Banco de Dados SQL](sql-database-cloud-migrate.md) para saber mais.

- Se você quiser carregar alguns dados em uma nova tabela de um arquivo CSV usando a ferramenta da linha de comando BCP, consulte [Carregando dados no Banco de Dados SQL a partir de um arquivo CSV usando o BCP](sql-database-load-from-csv-with-bcp.md).


## Recursos adicionais

[O que é o Banco de Dados SQL?](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->