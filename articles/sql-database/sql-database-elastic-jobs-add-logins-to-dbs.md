<properties 
	pageTitle="Como adicionar usuários a um pool de bancos de dados elásticos" 
	description="Você deve adicionar um usuário com privilégios para cada banco de dados do pool" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh" />

# Como adicionar usuários a um pool de bancos de dados elástico

Os **trabalhos de banco de dados elástico** permitem que você execute o mesmo script em todos os bancos de dados de um [pool de bancos de dados elástico](sql-database-elastic-pool.md). Para executar o script, é necessário adicionar um usuário com as permissões apropriadas a cada banco de dados no pool. Para saber mais, confira [Gerenciando bancos de dados e logons no SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) ou [Adicionando usuários ao Banco de Dados SQL do Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Pré-requisitos
* [Criar um pool de banco de dados elástico (visualização)](sql-database-elastic-pool-portal.md)
* Instalar os [componentes de trabalho elástico](sql-database-elastic-jobs-service-installation.md). 

## Como adicionar usuários aos bancos de dados

1.	Primeiro, conecte-se ao **mestre** do servidor de Banco de Dados SQL, onde os bancos de dados em seu pool de bancos de dados elástico reside e crie um novo logon usando as mesmas credenciais fornecidas ao instalar **trabalhos de banco de dados elástico**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Faça logon em cada banco de dados no pool e crie um usuário com o mesmo nome e senha. O usuário deve ter permissões suficientes para executar o trabalho. Esse código deve ser executado em cada banco de dados.

		CREATE USER admin1 FROM LOGIN login1;
		
3. O usuário também deve ter permissões suficientes para executar o script especificado para o trabalho. Use o procedimento **sp\_addrolemember** para fornecer ao usuário as permissões mínimas necessárias para que o script execute com êxito.

## Próximas etapas

Execute um trabalho no pool de banco de dados elástico. Consulte [Criando e gerenciando trabalhos de banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=August15_HO6-->