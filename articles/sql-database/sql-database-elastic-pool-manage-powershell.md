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
    ms.date="05/10/2016"
    ms.author="sidneyh"/>

# Monitorar e gerenciar um pool de banco de dados elástico com o PowerShell 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gerenciar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando cmdlets do PowerShell.

Para ver os códigos de erro comuns, confira [Códigos de erro de SQL para aplicativos clientes do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).

Valores para os pools podem ser encontrados em [limites de armazenamento e eDTU](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

## Pré-requisitos

* Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
* Os pools de banco de dados elásticos só estão disponíveis em servidores do Banco de Dados SQL V12. Se você tiver um servidor de Banco de Dados SQL V11, [use o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma única etapa.


## Mover um banco de dados para um pool elástico

Você pode mover um banco de dados para dentro ou para fora de um pool com [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx).

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Alterar as configurações de desempenho de um pool

Quando o desempenho for afetado, você pode alterar as configurações do pool para acomodar o crescimento. Use o cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx). Defina o parâmetro - Dtu para as eDTUs por pool. Consulte em [limites de armazenamento e eDTU](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) os valores possíveis.

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Obter o status das operações de pool

Criar um pool pode levar tempo. Para acompanhar o status das operações de pool, incluindo a criação e as atualizações, use o cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Obter o status de movimentação de um banco de dados elástico para dentro e fora de um pool

Mover um banco de dados pode levar tempo. Acompanhe o status da movimentação usando o cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Obter dados de uso de recursos para um pool

Métricas que podem ser recuperadas como uma porcentagem do limite do pool de recursos:


| Nome da métrica | Descrição |
| :-- | :-- |
| cpu\_percent | Média de utilização da computação em percentual do limite do pool. |
| physical\_data\_read\_percent | Média de utilização de E/S em percentual do limite do pool. |
| log\_write\_percent | Média de utilização dos recursos de gravação em percentual do limite do pool. | 
| DTU\_consumption\_percent | Média de utilização de eDTUs em percentual do limite de eDTUs do pool | 
| storage\_percent | Média de utilização do armazenamento em percentual do limite de armazenamento do pool. |  
| workers\_percent | Máximo de trabalhos (solicitações) simultâneos em percentual, com base no limite do pool. |  
| sessions\_percent | Número máximo de sessões simultâneas em percentual, com base no limite do pool. | 
| eDTU\_limit | Configuração atual de DTUs máximas do pool elástico para este pool elástico durante este intervalo. |
| storage\_limit | Configuração atual de limite máximo de armazenamento do pool elástico para este pool elástico em megabytes durante este intervalo. |
| eDTU\_used | Média de eDTUs usadas pelo pool neste intervalo. |
| storage\_used | Média de armazenamento usado pelo pool neste intervalo em bytes |

**Períodos de retenção/granularidade das métricas:**

* Os dados retornarão com uma granularidade de cinco minutos.  
* A retenção de dados é de 14 dias.  

Esse cmdlet e a API limitam o número de linhas que podem ser recuperadas em uma chamada para 1000 linhas (cerca de três dias de dados a uma granularidade de cinco minutos). Mas esse comando pode ser chamado várias vezes com intervalos de tempo de início/término diferentes para recuperar mais dados

Para recuperar as métricas:

	$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## Obter dados de uso de recursos para um banco de dados elástico

Essas APIs são as mesmas que as APIs (V12) atuais usadas para monitorar a utilização de recursos de um banco de dados autônomo, exceto pelas seguinte diferença semântica.

Para essa API, as métricas recuperadas são expressadas como um percentual das eDTUs máximas (ou capacidade equivalente para a métrica subjacente, como CPU, ES, etc.) definida para esse pool. Por exemplo, 50% da utilização de qualquer uma dessas métricas indica que o consumo do recursos específico é de 50% do limite por banco de dados desse recurso no pool pai.

Para recuperar as métricas:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## Coletar e monitorar dados de uso de recursos em vários pools em uma assinatura

Quando você tiver um grande número de bancos de dados em uma assinatura, é complicado monitorar cada pool elástico separadamente. Em vez disso, os cmdlets do PowerShell do Banco de Dados SQL e consultas T-SQL podem ser combinadas para coletar dados de uso de recursos de vários pools e bancos de dados para monitoramento e análise de uso de recursos. Uma [implementação de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) de tal um conjunto de scripts do powershell podem ser encontrados no repositório de exemplos do SQL Server do GitHub junto com a documentação sobre o que ele faz e como usá-lo.

Para usar esta implementação de exemplo, siga as etapas listadas abaixo.


1. Baixe os [scripts e documentação](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifique os scripts para seu ambiente. Especifique um ou mais servidores nos quais os pools elásticos são hospedados.
3. Especifique um banco de dados de telemetria no qual as métricas coletadas serão armazenadas. 
4. Personalize o script para especificar a duração da execução de scripts.

Em um alto nível, os scripts fazem o seguinte:

*	Enumera todos os servidores em uma determinada assinatura do Azure (ou uma lista de servidores específicos).
*	Executa um trabalho em segundo plano para cada servidor. O trabalho é executado em um loop em intervalos regulares e coleta dados de telemetria de todos os pools no servidor. Em seguida, ele carrega os dados coletados no banco de dados de telemetria especificado.
*	Enumera uma lista de bancos de dados em cada pool para coletar os dados de uso de recursos do banco de dados. Em seguida, ele carrega os dados coletados no banco de dados de telemetria.

As métricas coletadas no banco de dados de telemetria podem ser analisadas para monitorar a integridade dos pools elásticos e os bancos de dados nele. O script também instala uma TVF (função Tabela-Valor) predefinida no banco de dados de telemetria para ajudar a agregar as métricas para um período específico. Por exemplo, os resultados da TVF podem ser usados para mostrar "principais pools elásticos N” com a utilização de eDTU máxima em uma determinada janela de tempo". Outra opção é usar ferramentas analíticas como o Excel ou o Power BI para consultar e analisar os dados coletados.

## Exemplo: recuperar as métricas de consumo de recursos para um pool e seus bancos de dados

Este exemplo recupera as métricas de consumo para um determinado pool elástico e todos os seus bancos de dados. Os dados coletados são formatados e gravados em um arquivo formatado. csv. O arquivo pode ser navegado com o Excel.

	$subscriptionId = '<Azure subscription id>'	      # Azure subscription ID
	$resourceGroupName = '<resource group name>'             # Resource Group
	$serverName = <server name>                              # server name
	$poolName = <elastic pool name>                          # pool name
		
	# Login to Azure account and select the subscription.
	Login-AzureRmAccount
	Set-AzureRmContext -SubscriptionId $subscriptionId
	
	# Get resource usage metrics for an elastic pool for the specified time interval.
	$startTime = '4/27/2016 00:00:00'  # start time in UTC
	$endTime = '4/27/2016 01:00:00'    # end time in UTC
	
	# Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
	$poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
	$poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
	
	# Get the list of databases in this pool.
	$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
	
	# Get resource usage metrics for a database in an elastic database for the specified time interval.
	$dbMetrics = @()
	foreach ($db in $dbList)
	{
	    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
	    $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
	}
	
	#Optionally you can format the metrics and output as .csv file using the following script block.
	$command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
	}
	
	# Format and output pool metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
	
	# Format and output database metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## Latência de operações do pool elástico

- Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
- A alteração do limite de eDTUs por pool depende da quantidade total de espaço usado por todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.

## Migrar de servidores V11 para V12

Os cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorar uma atualização para o Banco de Dados SQL do Azure da V11 para a V12 ou de qualquer outra versão anterior à V12.

- [Atualização para o Banco de Dados SQL V12 com o PowerShell](sql-database-upgrade-server-powershell.md)

Para ver a documentação de referência sobre esses cmdlets do PowerShell, confira:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


O cmdlet Stop- significa cancelar, e não pausar. Não há como retomar uma atualização, a não ser começá-la novamente. O cmdlet Stop- limpa e libera todos os recursos apropriados.

## Próximas etapas

- [Criar trabalhos elásticos](sql-database-elastic-jobs-overview.md) Os trabalhos elásticos permitem executar scripts T-SQL em vários bancos de dados no pool.
- Consulte [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): use as ferramentas de banco de dados elástico para escalar horizontalmente, mover os dados e consultar ou criar transações.

<!---HONumber=AcomDC_0511_2016-->