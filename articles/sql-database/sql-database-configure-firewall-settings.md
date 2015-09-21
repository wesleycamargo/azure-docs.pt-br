<properties
	pageTitle="Como definir as configurações de firewall | Microsoft Azure"
	description="Configure o firewall para endereços IP que acessam bancos de dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# Como definir as configurações de firewall no Banco de Dados SQL


O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

**Importante** Para permitir que aplicativos do Azure se conectem ao servidor de banco de dados, as conexões do Azure devem ser habilitadas. Para saber mais sobre regras de firewall e como habilitar conexões no Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)


## Regras de firewall no nível de servidor

As regras de firewall no nível de servidor podem ser criadas e gerenciadas por meio do Portal de Gerenciamento do Microsoft Azure, Transact-SQL, PowerShell do Azure ou da API REST.

### Gerenciar regras de firewall no nível de servidor pelo novo Portal do Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Gerenciar regras de firewall no nível de servidor pelo Portal de Gerenciamento 

1. No Portal de Gerenciamento, clique em **Bancos de Dados SQL**. Todos os bancos de dados e os servidores correspondentes são listados aqui.
2. Clique em **Servidores** na parte superior da página.
3. Clique na seta ao lado do servidor para o qual deseja gerenciar as regras de firewall.
4. Clique em **Configurar** na parte superior da página.

	*  Para adicionar o computador atual, clique em Adicionar a Endereços IP Permitidos.
	*  Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final.
	*  Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
	*  Para excluir uma regra existente, passe o mouse sobre a regra até que o X apareça no final da linha. Clique no X para remover a regra.
5. Clique em **Salvar** na parte inferior da página para salvar as alterações.

## Gerenciar regras de firewall no nível de servidor pelo Transact-SQL

1. Inicie uma janela de consulta por meio do Portal de Gerenciamento ou SQL Server Management Studio.
2. Verifique se você está conectado ao banco de dados mestre.
3. As regras de firewall no nível de servidor podem ser selecionadas, criadas, atualizadas ou excluídas de dentro da janela de consulta.
4. Para criar ou atualizar regras de firewall no nível de servidor, execute o procedimento armazenado sp\_set\_firewall\_rule. O exemplo a seguir habilita um intervalo de endereços IP no servidor Contoso.<br/>Comece vendo quais regras já existem.

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	Em seguida, adicione uma regra de firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Para excluir uma regra de firewall no nível de servidor, execute o procedimento armazenado sp\_delete\_firewall\_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Gerenciar regras de firewall no nível de servidor pelo PowerShell do Azure
1. Inicie o PowerShell do Azure.
2. As regras de firewall no nível de servidor podem ser criadas, atualizadas e excluídas usando o PowerShell do Azure. 

	Para criar uma nova regra de firewall no nível de servidor, execute o cmdlet New-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir permite um intervalo de endereços IP no servidor Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Para modificar uma regra de firewall existente no nível de servidor, execute o cmdlet Set-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Para excluir uma regra de firewall existente no nível de servidor, execute o cmdlet Remove-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
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
 
## Regras de firewall no nível de banco de dados

1. Depois de criar um firewall no nível de servidor para seu endereço IP, inicie uma janela de consulta por meio do Portal de Gerenciamento ou SQL Server Management Studio.
2. Conecte-se ao banco de dados para o qual você deseja criar uma regra de firewall no nível de banco de dados.

	Para criar uma nova regra ou atualizar uma regra de firewall existente no nível de banco de dados, execute o procedimento armazenado sp\_set\_database\_firewall\_rule. O exemplo a seguir cria uma nova regra de firewall denominada ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Para excluir uma regra de firewall existente no nível de banco de dados, execute o procedimento armazenado sp\_delete\_database\_firewall\_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Gerenciar regras de firewall usando a API REST do Gerenciamento de Serviços

* [Criar uma regra de firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Excluir regra de firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obter regra de firewall](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Listar regras de firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## Gerenciar regras de firewall usando o PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Próximas etapas

Para ver um tutorial sobre como criar um banco de dados, confira [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Diretrizes para a conexão com o Banco de Dados SQL do Azure de forma programática](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber como navegar para os bancos de dados, confira [Gerenciando bancos de dados e logons em um Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Sept15_HO2-->