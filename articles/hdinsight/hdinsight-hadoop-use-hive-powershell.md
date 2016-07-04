<properties
   pageTitle="Usar o Hive do Hadoop com o PowerShell no HDInsight | Microsoft Azure"
   description="Usar o PowerShell para executar consultas Hive com Hadoop no HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/16/2016"
   ms.author="larryfr"/>

#Executar consultas Hive usando o PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de como usar o Azure PowerShell no modo de Grupo de Recursos do Azure para executar consultas do Hive em um Hadoop no cluster HDInsight.

> [AZURE.NOTE] Esse documento não fornece uma descrição detalhada do que as instruções HiveQL que usadas nos exemplos fazem. Para obter informações sobre o HiveQL usado neste exemplo, veja [Usar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).


**Pré-requisitos**

Para concluir as etapas neste artigo, você precisará do seguinte.

- **Um cluster do Azure HDInsight (Hadoop no HDInsight, baseado em Windows ou Linux)**
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##Executar consultas do Hive usando o Azure PowerShell

O PowerShell do Azure fornece *cmdlets* que lhe permitem executar remotamente as consultas Hive no HDInsight. Internamente, isso é feito por meio de chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do Hive em um cluster HDInsight remoto:

* **Add-AzureRmAccount**: autentica o Azure PowerShell para a sua assinatura do Azure

* **New-AzureRmHDInsightHiveJobDefinition**: cria uma nova *definição de trabalho* usando as instruções HiveQL especificadas

* **Start-AzureRmHDInsightJob**: envia a definição do trabalho para o HDInsight, inicia o trabalho e retorna um objeto *job* que pode ser usado para verificar o status do trabalho

* **Wait-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguardará até que o trabalho seja concluído ou o tempo de espera seja excedido.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho

* **Invoke-AzureRmHDInsightHiveJob**: usado para executar instruções HiveQL. Isso bloqueará a consulta até que seja concluída e, em seguida, retornará os resultados

* **Use-AzureRmHDInsightCluster**: define o cluster atual a ser usado para o comando **Invoke-AzureRmHDInsightHiveJob**

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como **hivejob.ps1**. Você deve substituir **CLUSTERNAME** pelo nome do seu cluster HDInsight.

		#Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra um novo prompt de comando do **PowerShell do Azure**. Altere os diretórios para o local do arquivo **hivejob.ps1** e use o seguinte comando para executar o script:

		.\hivejob.ps1

    Quando o script for executado, você receberá uma solicitação para inserir as credenciais da conta de Administrador/HTTPS para o cluster. Também receberá uma solicitação para fazer logon em sua assinatura do Azure.
    
7. Quando o trabalho for concluído, ele deverá retornar informações semelhantes às seguintes:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, **Invoke-Hive** pode ser usado para executar uma consulta e aguardar a resposta. Use os seguintes comandos, substituindo **CLUSTERNAME** pelo nome do cluster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

	A saída terá a aparência a seguir:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Para consultas HiveQL mais longas, é possível usar o cmdlet **Here-Strings** do PowerShell do Azure ou arquivos de script HiveQL. Os fragmentos a seguir mostram como usar o cmdlet **Invoke-Hive** para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasb://.
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obter mais informações sobre **Here-Strings**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Usando Here-Strings do Windows PowerShell</a>.

##Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **hivejob.ps1**, salve-o e execute-o novamente.

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isso retorna as informações gravadas em STDERR para o servidor ao executar o trabalho e pode ajudar a determinar por que o trabalho está falhando.

##Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0622_2016-->