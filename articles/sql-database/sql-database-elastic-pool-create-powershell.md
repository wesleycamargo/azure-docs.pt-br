<properties 
    pageTitle="Criar um pool de banco de dados elástico (PowerShell) | Microsoft Azure" 
    description="Saiba como usar o PowerShell para expandir recursos do Banco de Dados SQL do Azure criando um pool de banco de dados elástico para gerenciar vários bancos de dados." 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Criar um pool de banco de dados elástico (PowerShell) 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando cmdlets do PowerShell

Para obter os códigos de erro comuns, veja [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma única etapa.


Você precisa executar o Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).



## Criar um pool de banco de dados elástico

O cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cria um pool de banco de dados elásticos.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Criar um novo banco de dados elástico em um pool de banco de dados elástico

Para criar um novo banco de dados diretamente dentro de um pool, use o cmdlet [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Mover um banco de dados autônomo em um pool de banco de dados elástico

Para mover um banco de dados existente para um pool, use o cmdlet [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) e defina o parâmetro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Exemplo de criação de um pool de banco de dados elástico do PowerShell

Essa script criará um novo servidor, para que quando ele solicitar um nome de usuário e senha, insira um logon de administrador e uma senha de administrador para o novo servidor (não suas credenciais do Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Próximas etapas

- [Gerenciar o pool](sql-database-elastic-pool-manage-powershell.md)
- [Criar trabalhos elásticos](sql-database-elastic-jobs-overview.md) Os trabalhos elásticos facilitam a execução dos scripts T-SQL em vários bancos de dados no pool.


## Referência de banco de dados elástico

Para saber mais sobre bancos de dados elásticos e sobre pools de banco de dados elásticos, confira a [Referência de banco de dados elásticos](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0323_2016-->