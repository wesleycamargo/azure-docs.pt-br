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
	ms.date="04/14/2016"
	ms.author="rickbyh;carlrab"/>


# Como configurar um firewall do Banco de Dados SQL do Azure usando o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

O SQL Server do Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor lógico do SQL Server do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [AZURE.IMPORTANT] Para permitir que os aplicativos do Azure se conectem ao seu SQL Server do Azure, as conexões do Azure devem estar habilitadas. Para entender como as regras de firewall funcionam, consulte [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Talvez seja necessário abrir algumas portas TCP adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

[AZURE.INCLUDE [Criar Banco de Dados SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure

Repita as etapas para gerenciar as regras de firewall de nível de servidor.

- Para adicionar o computador atual, clique em Adicionar IP do cliente.
- Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final.
- Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
- Para excluir uma regra existente, passe o mouse sobre a regra até que o X apareça no final da linha. Clique no X para remover a regra.

Clique em **Salvar** para salvar as alterações.

## Próximas etapas

Uma regra de firewall de servidor afeta todos os Bancos de Dados SQL no SQL Server do Azure. Para configurar uma regra de firewall no nível de banco de dados que afetará um banco de dados individual, consulte [sp\_set\_database\_firewall\_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx") (sp\_set\_database\_firewall\_rule (Banco de Dados SQL do Azure)).

Para ver um tutorial sobre como criar um banco de dados, confira [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Diretrizes para a conexão com o Banco de Dados SQL do Azure de forma programática](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber como navegar para os bancos de dados, confira [Gerenciando bancos de dados e logons em um Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0420_2016-->