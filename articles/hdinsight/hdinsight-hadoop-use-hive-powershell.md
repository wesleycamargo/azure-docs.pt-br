<properties
   pageTitle="Usar o Hive do Hadoop com o PowerShell no HDInsight | Microsoft Azure"
   description="Usar o PowerShell para executar consultas Hive com Hadoop no HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Executar consultas Hive usando o PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de uso do PowerShell do Azure para executar consultas do Hive em um Hadoop no cluster HDInsight.

> [AZURE.NOTE]Esse documento não fornece uma descrição detalhada do que as instruções HiveQL que usadas nos exemplos fazem. Para obter informações sobre o HiveQL usado neste exemplo, consulte [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md).


##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

- **Um cluster do Azure HDInsight (Hadoop no HDInsight, baseado em Windows ou Linux)**
- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##<a id="powershell"></a>Executar consultas Hive usando o PowerShell do Azure

O PowerShell do Azure fornece *cmdlets* que lhe permitem executar remotamente as consultas Hive no HDInsight. Internamente, isso é feito por meio de chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do Hive em um cluster HDInsight remoto:

* **Add-AzureAccount**: autentica o PowerShell do Azure para sua assinatura do Azure

* **New-AzureHDInsightHiveJobDefinition**: cria uma nova *definição de trabalho* usando as instruções HiveQL especificadas

* **Start-AzureHDInsightJob**: envia a definição do trabalho para o HDInsight, inicia o trabalho e retorna um objeto *job* que pode ser usado para verificar o status do trabalho

* **Wait-AzureHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguardará até que o trabalho seja concluído ou o tempo de espera seja excedido.

* **Get-AzureHDInsightJobOutput**: usado para recuperar a saída do trabalho

* **Invoke-Hive**: usado para executar instruções HiveQL. Isso bloqueará a consulta até que seja concluída e, em seguida, retornará os resultados

* **Use-AzureHDInsightCluster**: define o cluster atual a ser usado para o comando **Invoke-Hive**

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como **hivejob.ps1**. Você deve substituir **CLUSTERNAME** pelo nome do seu cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Abra um novo prompt de comando do **PowerShell do Azure**. Altere os diretórios para o local do arquivo **hivejob.ps1** e use o seguinte comando para executar o script:

		.\hivejob.ps1

7. Quando o trabalho for concluído, ele deverá retornar informações semelhantes às seguintes:

		Display the standard output...
		[ERROR]	3

4. Como mencionado anteriormente, **Invoke-Hive** pode ser usado para executar uma consulta e aguardar a resposta. Use os seguintes comandos, substituindo **CLUSTERNAME** pelo nome do cluster:

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	A saída terá a aparência a seguir:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]Para consultas HiveQL mais longas, é possível usar o cmdlet **Here-Strings** do PowerShell do Azure ou arquivos de script HiveQL. Os fragmentos a seguir mostram como usar o cmdlet **Invoke-Hive** para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasb://.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obter mais informações sobre **Here-Strings**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Usando Here-Strings do Windows PowerShell</a>.

##<a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **hivejob.ps1**, salve-o e execute-o novamente.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Isso retorna as informações gravadas em STDERR para o servidor ao executar o trabalho e pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->