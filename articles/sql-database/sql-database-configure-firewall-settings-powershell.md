<properties
	pageTitle="Como configurar um firewall do Banco de Dados SQL do Azure | Microsoft Azure"
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
	ms.date="05/09/2016"
	ms.author="sstein"/>


# Como configurar um firewall do Banco de Dados SQL do Azure usando o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Talvez seja necessário abrir algumas portas TCP adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Criar regras de firewall de servidor

As regras de firewall no nível de servidor podem ser criadas, atualizadas e excluídas usando o PowerShell do Azure.

Para criar uma nova regra de firewall no nível de servidor, execute o cmdlet New-AzureRmSqlServerFirewallRule. O exemplo a seguir permite um intervalo de endereços IP no servidor Contoso.
 
    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'		
 
Para modificar uma regra de firewall existente no nível de servidor, execute o cmdlet Set-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.
 
    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para excluir uma regra de firewall existente no nível de servidor, execute o cmdlet Remove-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## Gerenciar regras de firewall usando o PowerShell

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)
 
## Próximas etapas

Para ver um tutorial sobre como criar um banco de dados, confira [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Diretrizes para a conexão com o Banco de Dados SQL do Azure de forma programática](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber como navegar para os bancos de dados, confira [Gerenciando bancos de dados e logons em um Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0518_2016-->