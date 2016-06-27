<properties
	pageTitle="Como configurar um firewall do Banco de Dados SQL | Microsoft Azure"
	description="Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/26/2016"
	ms.author="rickbyh"/>


# Como configurar um firewall do Banco de Dados SQL do Azure usando TSQL


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Talvez seja necessário abrir algumas portas TCP adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Gerenciar regras de firewall no nível de servidor pelo Transact-SQL

Somente o logon da entidade de segurança no nível do servidor ou o administrador do Azure Active Directory pode criar uma regra de firewall de nível de servidor usando o Transact-SQL.

1. Inicie uma janela de consulta e conecte-se ao banco de dados mestre virtual usando o SQL Server Management Studio.
2. As regras de firewall no nível de servidor podem ser selecionadas, criadas, atualizadas ou excluídas de dentro da janela de consulta.
3. Para criar ou atualizar regras de firewall no nível de servidor, execute o procedimento armazenado sp\_set\_firewall\_rule. O exemplo a seguir habilita um intervalo de endereços IP no servidor Contoso.<br/>Comece vendo quais regras já existem.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	Em seguida, adicione uma regra de firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Para excluir uma regra de firewall no nível de servidor, execute o procedimento armazenado sp\_delete\_firewall\_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Para saber mais sobre esses procedimentos armazenados, confira [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## Regras de firewall no nível de banco de dados

Somente um usuário de banco de dados com a permissão **CONTROL** no banco de dados (como o proprietário do banco de dados) pode criar uma regra de firewall no nível de banco de dados.

1. Depois de criar um firewall no nível de servidor para seu endereço IP, inicie uma janela de consulta por meio do Portal Clássico ou SQL Server Management Studio.
2. Conecte-se ao banco de dados para o qual você deseja criar uma regra de firewall no nível de banco de dados.

	Para criar uma nova regra ou atualizar uma regra de firewall existente no nível de banco de dados, execute o procedimento armazenado sp\_set\_database\_firewall\_rule. O exemplo a seguir cria uma nova regra de firewall denominada ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Para excluir uma regra de firewall existente no nível de banco de dados, execute o procedimento armazenado sp\_delete\_database\_firewall\_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para obter mais informações sobre esses procedimentos armazenados, consulte [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## Próximas etapas

Para um tutorial sobre como criar um banco de dados, consulte [Criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Exemplos de código do cliente de início rápido do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender como navegar para bancos de dados, consulte [Gerenciar a segurança de logon e o acesso ao banco de dados](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!---HONumber=AcomDC_0615_2016-->