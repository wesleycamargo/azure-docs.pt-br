<properties 
	pageTitle="Como adicionar usuários a um pool de bancos de dados elásticos" 
	description="Você deve adicionar um usuário com privilégios para cada banco de dados do pool" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Como adicionar usuários a um pool de bancos de dados elástico

O recurso **trabalhos de Banco de Dados Elástico** (visualização) permite que você execute um script Transact-SQL em um grupo de bancos de dados incluindo uma coleção de bancos de dados definida de modo personalizado, um **pool de Banco de Dados Elástico** ou um **conjunto de fragmentos de banco de dados elástico** no Banco de Dados SQL do Azure. Para executar o script, um usuário com as permissões apropriadas deverá ser adicionado a cada banco de dados em que o trabalho será executado. Para saber mais, confira [Gerenciando bancos de dados e logons no SQL do Azure](sql-database-manage-logins.md) ou [Adicionando usuários ao Banco de Dados SQL do Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Pré-requisitos
* Instalar os [componentes de trabalho elástico](sql-database-elastic-jobs-service-installation.md). 

## Como adicionar usuários aos bancos de dados

1.	Primeiro, conecte-se ao **mestre** do servidor de Banco de Dados SQL, onde os bancos de dados em seu pool de bancos de dados elástico reside e crie um novo logon usando as mesmas credenciais fornecidas ao instalar **trabalhos de banco de dados elástico**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Faça logon em cada banco de dados no pool e crie um usuário com o mesmo nome e senha. O usuário deve ter permissões suficientes para executar o trabalho. Esse código deve ser executado em cada banco de dados.

		CREATE USER admin1 FROM LOGIN login1;
		
3. O usuário também deve ter permissões suficientes para executar o script especificado para o trabalho. Use o procedimento [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) para fornecer ao usuário as permissões mínimas necessárias para que o script seja executado com êxito.

## Próximas etapas

Para criar e gerenciar trabalhos com o portal do Azure, consulte [Criando e gerenciando trabalhos de banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md). Para criar trabalhos com PowerShell, [Criar e gerenciar trabalhos de banco de dados elástico de Banco de Dados SQL usando o PowerShell (visualização)](sql-database-elastic-jobs-powershell.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->