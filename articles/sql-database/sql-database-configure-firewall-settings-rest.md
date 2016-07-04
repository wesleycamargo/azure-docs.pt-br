<properties
	pageTitle="Regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST | Microsoft Azure"
	description="Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/15/2016"
	ms.author="sstein"/>


#  Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Talvez seja necessário abrir algumas portas TCP adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Gerenciar regras de firewall no nível de servidor pela API REST
1. O gerenciamento das regras de firewall por meio da API REST deve ser autenticado. Para obter informações, confira Autenticando solicitações do Gerenciamento de Serviços.
2. As regras no nível de servidor podem ser criadas, atualizadas ou excluídas usando a API REST

	Para criar ou atualizar uma regra de firewall no nível de servidor, execute o método POST usando o seguinte:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Corpo da solicitação

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Para remover uma regra de firewall existente no nível de servidor, execute o método DELETE usando o seguinte:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule


## Gerenciar regras de firewall usando a API REST do Gerenciamento de Serviços

* [Criar uma regra de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Excluir regra de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obter regra de firewall](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Listar regras de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Próximas etapas

Para ver um artigo sobre como usar o Transact-SQL para criar regras de firewall no nível de banco de dados e no nível de servidor, confira [Configurar um firewall no nível de banco de dados e no nível de servidor do Banco de Dados SQL do Azure usando T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para ver artigos sobre como criar regras de firewall no nível de servidor usando outros métodos, confira:

- [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)

Para ver um tutorial sobre como criar um banco de dados, confira [Criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Exemplos de código do cliente de início rápido do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender como navegar para bancos de dados, confira [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Gerenciar segurança de acesso ao banco de dados e de logon).


## Recursos adicionais

- [Protegendo o banco de dados](sql-database-security.md)
- [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0622_2016-->