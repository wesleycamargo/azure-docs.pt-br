<properties
	pageTitle="Gerenciar o Banco de Dados SQL do Azure com o PowerShell | Microsoft Azure"
	description="Gerenciamento de banco de dados SQL do Azure com o PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="sstein"/>

# Gerenciar o Banco de Dados SQL do Azure com o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Este tópico fornece comandos do PowerShell para executar várias tarefas do Banco de Dados SQL do Azure.

[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]


## Criar um grupos de recursos

Crie um grupo de recursos que conterá o servidor. Você pode editar o próximo comando para usar qualquer local válido.

Para obter uma lista de locais válidos de servidor do Banco de Dados SQL, execute o seguinte cmdlet:

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Caso já tenha um grupo de recursos, você poderá pular para a próxima seção (“Criar um servidor”) ou então poderá editar e executar o seguinte comando para criar um novo grupo de recursos:

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Criar um servidor

Para criar um novo servidor V12, use o cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Substitua *server12* pelo nome do seu servidor. Se o nome do servidor já existir, você obterá uma mensagem de erro. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Os detalhes do servidor e o prompt do PowerShell são exibidos após a criação do servidor. Você pode editar o comando para usar qualquer local válido.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Quando você executar esse comando, serão solicitados seu nome de usuário e senha. Não insira suas credenciais do Azure aqui. Ao contrário, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.

## Criar uma regra de firewall de servidor

Para criar uma regra de firewall para acessar o servidor, use o comando [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu cliente.

Se o servidor precisar permitir acesso a outros serviços do Azure, adicione a opção **-AllowAllAzureIPs**. Isso adiciona uma regra de firewall especial e permite que todo o tráfego do Azure acesse o servidor.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Criar um banco de dados SQL

Para criar um banco de dados, use o comando [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). É necessário um servidor para criar um banco de dados. O exemplo a seguir cria um banco de dados SQL denominado TestDB12. O banco de dados é criado como um banco de dados Padrão S1.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Alterar o nível de desempenho do banco de dados SQL

É possível escalar o banco de dados vertical ou horizontalmente com o comando [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). O exemplo a seguir escala verticalmente um banco de dados SQL denominado TestDB12 de seu nível de desempenho atual para um nível Padrão S3.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Excluir um banco de dados SQL

Você pode excluir um banco de dados SQL com o comando [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). O exemplo a seguir exclui um banco de dados SQL denominado TestDB12.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Excluir um servidor

Também é possível excluir um servidor com o comando [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). O exemplo a seguir exclui um servidor chamado server12.


>[AZURE.NOTE]  A operação de exclusão é assíncrona e pode levar algum tempo. Verifique se a operação foi concluída antes de executar operações adicionais que dependem de o servidor ser completamente excluído (por exemplo, criar um novo servidor com o mesmo nome).


	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"




## Próximas etapas

Combinar comandos e automatizar. Por exemplo, para criar um servidor, uma regra de firewall e um banco de dados, substitua tudo que está entre aspas, incluindo os caracteres < e > pelos seus valores:


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informações relacionadas

- [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0803_2016-->