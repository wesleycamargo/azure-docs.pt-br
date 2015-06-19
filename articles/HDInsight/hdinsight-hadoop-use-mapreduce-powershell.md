<properties
   pageTitle="Usar o MapReduce e o PowerShell com Hadoop | Microsoft Azure"
   description="Aprenda a usar o PowerShell para executar trabalhos MapReduce remotamente com Hadoop no HDInsight."
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Executar consultas Hive com Hadoop no HDInsight usando o PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Esse documento fornece um exemplo de uso do PowerShell do Azure para executar um trabalho MapReduce em um Hadoop no cluster HDInsight.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do Azure HDInsight (Hadoop no HDInsight, baseado em Windows ou Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">PowerShell do Azure</a>

##<a id="powershell"></a>Executar um trabalho MapReduce usando o PowerShell do Azure

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos MapReduce no HDInsight. Internamente, isso é feito por meio de chamadas REST para <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar trabalhos MapReduce em um cluster HDInsight remoto.

* **Add-AzureAccount**: autentica o PowerShell do Azure para sua assinatura do Azure

* **New-AzureHDInsightMapReduceJobDefinition**: cria uma nova *definição de trabalho* usando as informações especificadas do MapReduce

* **Start-AzureHDInsightJob**: envia a definição do trabalho para o HDInsight, inicia o trabalho e retorna um objeto *job* que pode ser usado para verificar o status do trabalho

* **Wait-AzureHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.

* **Get-AzureHDInsightJobOutput**: usado para recuperar a saída do trabalho

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **mapreducejob.ps1**. Você deve substituir **CLUSTERNAME** pelo nome do seu cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. Abra um novo prompt de comando do **PowerShell do Azure**. Altere os diretórios para o local do arquivo **mapreducejob.ps1** e use o seguinte comando para executar o script:

		.\mapreducejob.ps1

3. Quando o trabalho for concluído, você receberá uma saída semelhante ao seguinte:

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	Essa saída indica que o trabalho foi concluído com êxito.
	
	> [AZURE.NOTE]Se o **ExitCode** for um valor diferente de 0, consulte [Solução de problemas](#troubleshooting).

##<a id="results"></a>Exibir a saída do trabalho

O trabalho MapReduce armazenou os resultados da operação para o Armazenamento de Blob do Azure, no caminho **wasb:///example/data/WordCountOutput** especificado como um argumento para o trabalho. O Armazenamento de Blob do Azure é acessível pelo PowerShell do Azure, mas você deve saber o nome da conta de armazenamento, a chave e um contêiner que é usado pelo seu cluster HDInsight para acessar diretamente os arquivos.

Felizmente, você pode obter essas informações usando os seguintes cmdlets do PowerShell do Azure:

* **Get-AzureHDInsightCluster**: retorna informações sobre um cluster HDInsight, incluindo quaisquer contas de armazenamento associadas a ele. Sempre haverá uma conta de armazenamento padrão associada a um cluster.
* **New-AzureStorageContext**: após receber o nome da conta de armazenamento e a chave recuperada usando **Get-AzureHDInsightCluster**, retorna um objeto de contexto que pode ser usado para acessar a conta de armazenamento.
* **Get-AzureStorageBlob**: após receber um objeto de contexto e o nome do contêiner, retorna uma lista de blobs dentro do contêiner.
* **Get-AzureStorageBlobContent**: após receber um objeto de contexto, um caminho de arquivo e nome e um nome de contêiner (retornado por **Get-AzureHDinsightCluster**), baixa um arquivo do Armazenamento de Blob do Azure.

O exemplo a seguir recupera as informações de armazenamento, em seguida baixa a saída por meio **wasb:///example/data/WordCountOutput**. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]Este exemplo armazenará os arquivos baixados na pasta **example/data/WordCountOutput**, no diretório no qual você executa o script.

A saída do trabalho MapReduce é armazenada em arquivos com o nome *part-r-#####*. Abra o arquivo **example/data/WordCountOutput/part-r-00000** em um editor de texto para ver as palavras e contagens produzidas pelo trabalho.

> [AZURE.NOTE]Os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

##<a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para esse trabalho, adicione o seguinte comando ao final do arquivo **mapreducejob.ps1**, depois salve o arquivo e execute-o novamente.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

Isso retorna as informações que foram gravadas em STDERR no servidor quando você executou o trabalho, e pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

<!--HONumber=54--> 