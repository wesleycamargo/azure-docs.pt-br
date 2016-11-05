---
title: Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell | Microsoft Docs
description: Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein

---
# Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

O Banco de Dados SQL do Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL a fim de permitir seletivamente o acesso ao banco de dados.

> [!IMPORTANT]
> Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Se você estiver fazendo conexões dentro do limite de nuvem do Azure, terá que abrir algumas portas TCP adicionais. Para obter mais informações, veja a seção "V12 do Banco de Dados SQL: fora versus dentro" de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

[!INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Criar regras de firewall de servidor
As regras de firewall no nível de servidor podem ser criadas, atualizadas e excluídas usando o Azure PowerShell.

Para criar uma nova regra de firewall no nível de servidor, execute o cmdlet New-AzureRmSqlServerFirewallRule. O exemplo a seguir permite um intervalo de endereços IP no servidor Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Para modificar uma regra de firewall existente no nível de servidor, execute o cmdlet Set-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para excluir uma regra de firewall existente no nível de servidor, execute o cmdlet Remove-AzureSqlDatabaseServerFirewallRule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## Gerenciar regras de firewall usando o PowerShell
Você também pode usar o PowerShell para gerenciar regras de firewall. Para saber mais, consulte os tópicos a seguir:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)

## Próximas etapas
Para obter informações sobre como usar o Transact-SQL para criar regras de firewall no nível de banco de dados e no nível de servidor, consulte [Configurar um firewall no nível de banco de dados e no nível de servidor do Banco de Dados SQL do Azure usando T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para obter informações sobre como criar regras de firewall no nível de servidor usando outros métodos, consulte:

* [Configurar regras de firewall no nível do servidor do Banco de Dados SQL do Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)
* [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar um banco de dados, confira [Criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um Banco de Dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, consulte [Exemplos de código do cliente de início rápido do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para entender como navegar para bancos de dados, consulte [Gerenciar o acesso ao banco de dados e a segurança de logon](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## Recursos adicionais
* [Protegendo o banco de dados](sql-database-security.md)
* [Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0810_2016-->