<properties
    pageTitle="Criar um novo Pool de Banco de Dados Elástico com o PowerShell | Microsoft Azure"
    description="Saiba como usar o PowerShell para expandir recursos do Banco de Dados SQL do Azure criando um pool de banco de dados elástico escalonável para gerenciar vários bancos de dados."
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Criar um Pool de Banco de Dados Elástico com o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando cmdlets do PowerShell

Para obter os códigos de erro comuns, veja [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma única etapa.


Você precisa executar o Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

## Criar um novo pool

O cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cria um novo pool. Os valores de eDTU por pool e Dtus mínimo e máximo são restringidos pelo valor da camada de serviço (basic, standard ou premium). Confira [Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Criar um novo banco de dados elástico em um pool

Use o cmdlet [AzureRmSqlDatabase novo](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName** para o pool de destino. Para mover um banco de dados existente para um pool, confira [Mover um banco de dados para um pool elástico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Criar um pool e populá-lo com vários novos bancos de dados 

A criação de um grande número de bancos de dados em um pool pode levar tempo quando feito usando o portal ou cmdlets do PowerShell que criam apenas um banco de dados individual por vez. Para automatizar a criação em um novo pool, confira [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## Exemplo: criar um pool usando o PowerShell 

Esse script cria um novo grupo de recursos do Azure e um novo servidor. Quando solicitado, forneça um nome de usuário de administrador e uma senha para o novo servidor (e não as suas credenciais do Azure).

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
- [Criar trabalhos elásticos](sql-database-elastic-jobs-overview.md) Os trabalhos elásticos permitem a execução de scripts T-SQL em vários bancos de dados no pool.
- [Escalar horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástico para escalar horizontalmente.

<!---HONumber=AcomDC_0601_2016-->