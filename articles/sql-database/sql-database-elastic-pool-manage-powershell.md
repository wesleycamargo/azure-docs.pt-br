<properties 
    pageTitle="Gerenciar um pool de banco de dados elástico (PowerShell) | Microsoft Azure" 
    description="Saiba como usar o PowerShell para gerenciar um pool de banco de dados elástico."  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Monitorar, gerenciar e dimensionar um pool de banco de dados elástico com o PowerShell 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


Saiba como gerenciar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando cmdlets do PowerShell.

Para obter os códigos de erro comuns, veja [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma única etapa.

Você precisa executar o Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).




## Criar um novo banco de dados elástico em um pool

Para criar um novo banco de dados diretamente dentro de um pool, use o cmdlet [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Mover um banco de dados independente para um pool

Para mover um banco de dados existente para um pool, use o cmdlet [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) e defina o parâmetro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Alterar as configurações de desempenho de um pool

Para alterar as configurações de desempenho de um pool, use o cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Obter o status das operações de pool

É possível acompanhar o status das operações de pool, incluindo criação e atualizações, usando o cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Obter o status de movimentação de um banco de dados elástico para dentro e fora de um pool

É possível acompanhar o status das operações de banco de dados elástico, incluindo criação e atualizações, usando o cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Obter dados de uso de um pool

Métricas que podem ser recuperadas como uma porcentagem do limite do pool de recursos:

* Utilização média da CPU - cpu\_percent 
* Utilização média de ES - data\_io\_percent 
* Utilização média de log - log\_write\_percent 
* Utilização média da memória - memory\_percent 
* Utilização média de eDTU (como um valor máximo de utilização de CPU/ES/Log) – DTU\_percent 
* Número máximo de solicitações de usuário simultâneas (operadores) – max\_concurrent\_requests 
* Número máximo de sessões de usuário simultâneas – max\_concurrent\_sessions 
* Tamanho total de armazenamento para o pool elástico – storage\_in\_megabytes 


Períodos de retenção/granularidade das métricas:

* Os dados retornarão com uma granularidade de cinco minutos.  
* A retenção de dados é de 14 dias.  


Esse cmdlet e a API limitam o número de linhas que podem ser recuperadas em uma chamada para 1000 linhas (cerca de três dias de dados a uma granularidade de cinco minutos). Mas esse comando pode ser chamado várias vezes com intervalos de tempo de início/término diferentes para recuperar mais dados


Recupere as métricas:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenha mais dias repetindo a chamada e acrescentando os dados:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formate a tabela:

    $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Obter métricas do consumo de recursos de um banco de dados elástico

Essas APIs são as mesmas que as APIs (V12) atuais usadas para monitorar a utilização de recursos de um banco de dados autônomo, exceto para as seguintes diferenças semânticas

* Para essa API, as métricas recuperadas são expressadas como um percentual do databaseDtuMax (ou capacidade equivalente para a métrica subjacente, como CPU, ES, etc.) definida para esse pool. Por exemplo, 50% da utilização de qualquer uma dessas métricas indica que o consumo do recursos específico é de 50% do limite por banco de dados desse recurso no pool pai. 

Obtenha as métricas:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenha mais dias se for necessário repetindo a chamada e acrescentando os dados:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formate a tabela:

    $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Monitorar e gerenciar um exemplo de PowerShell de pool


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Próximas etapas

- [Criar trabalhos elásticos](sql-database-elastic-jobs-overview.md) Os trabalhos elásticos permitem a execução de scripts T-SQL em vários bancos de dados no pool.


## Referência de banco de dados elástico

Para saber mais sobre pools de banco de dados elásticos e bancos de dados elásticos, incluindo detalhes sobre APIs e erros, confira a [Referência de bancos de dados elásticos](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0330_2016-->