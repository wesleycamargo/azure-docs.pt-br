<properties
	pageTitle="Como: definir configurações de firewall | Microsoft Azure"
	description="Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure."
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
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# Como definir as configurações de firewall no Banco de Dados SQL usando o Portal do Azure


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


O Banco de Dados SQL do Microsoft Azure usa regras de firewall para permitir conexões com servidores e bancos de dados. Você pode definir configurações de firewall nos níveis de banco de dados e de servidor para o banco de dados de usuário ou mestre no seu servidor do Banco de Dados SQL do Azure a fim de permitir seletivamente o acesso ao banco de dados.

> [AZURE.IMPORTANT]Para permitir que os aplicativos do Azure se conectem ao seu servidor de banco de dados, as conexões do Azure devem estar habilitadas. Para obter mais informações sobre regras de firewall e como habilitar conexões do Azure, confira [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md). Talvez seja necessário abrir algumas portas TCP adicionais se estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, veja a seção **V12 do Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


### Gerenciar regras de firewall no nível de servidor pelo Novo Portal do Azure


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


## Próximas etapas

Para ver um tutorial sobre como criar um banco de dados, confira [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md). Para obter ajuda com a conexão a um banco de dados SQL do Azure por meio de aplicativos de software livre ou de terceiros, confira [Diretrizes para a conexão com o Banco de Dados SQL do Azure de forma programática](https://msdn.microsoft.com/library/azure/ee336282.aspx). Para saber como navegar para os bancos de dados, confira [Gerenciando bancos de dados e logons em um Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->