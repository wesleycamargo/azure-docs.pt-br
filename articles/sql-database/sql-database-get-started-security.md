<properties
	pageTitle="Tutorial do Banco de Dados SQL: Introdução à segurança"
	description="Saiba como criar contas de usuário para acessar e gerenciar um banco de dados."
	keywords=""
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
	ms.date="08/17/2016"
	ms.author="carlrab"/>

# Tutorial do Banco de Dados SQL: Criar contas de usuário do Banco de Dados SQL para acessar e gerenciar um banco de dados


> [AZURE.SELECTOR]
- [Tutorial de introdução](sql-database-get-started-security.md)
- [Conceder acesso](sql-database-manage-logins.md)

Neste tutorial, você aprenderá a usar o SQL Server Management Studio (SSMS) para:

- Faça logon no Banco de Dados SQL usando um logon de entidade no nível do servidor.
- Criar uma conta de usuário do Banco de Dados SQL.
- Conceda [permissões de db\_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) a um novo usuário de banco de dados SQL.
- Conecte-se a um Banco de Dados SQL com uma conta de usuário que não é uma entidade de nível de servidor.

[AZURE.INCLUDE [Logon](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Criar um servidor lógico do Banco de Dados SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Próximas etapas
Agora que você concluiu este tutorial do Banco de Dados SQL, criou uma conta de usuário e atribuiu permissões dbo a ela, está pronto para saber mais sobre a [Segurança do Banco de Dados SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0824_2016-->