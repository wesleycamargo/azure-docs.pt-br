<properties
   pageTitle="Usar o Pig do Hadoop no HDInsight | Azure"
   description="Saiba como enviar trabalhos do Pig para o Hadoop no HDInsight usando o PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Executar trabalhos do Pig usando o PowerShell

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de uso do PowerShell para enviar trabalhos do Pig para um Hadoop no cluster HDInsight. O Pig permite que você escreva trabalhos do MapReduce usando uma linguagem (Pig Latin) que modela transformações de dados, em vez das funções mapear e reduzir.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções Pig Latin usadas nos exemplos. Para obter informações sobre o Pig Latin usado neste exemplo, consulte <a href="../hdinsight-use-pig/" target="_blank">Usar o Pig com Hadoop no HDInsight</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (Windows ou Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">PowerShell do Azure</a>


##<a id="powershell"></a>Executar trabalhos do Pig usando o PowerShell

O PowerShell do Azure fornece  *cmdlets* que permitem executar remotamente trabalhos do Pig no HDInsight. Internamente, isso é feito por meio de chamadas REST para <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os seguintes cmdlets são usados ao executar trabalhos do Pig em um cluster do HDInsight remoto.

* **Add-AzureAccount** - Autentica o PowerShell para sua assinatura do Azure

* **New-AzureHDInsightPigJobDefinition** - Cria uma nova  *definição de trabalho* usando as instruções de Pig Latin especificadas

* **Start-AzureHDInsightJob** - Envia a definição do trabalho para o HDInsight, inicia o trabalho e retorna um objeto  *job* que pode ser usado para verificar o status do trabalho

* **Wait-AzureHDInsightJob** - Usa o objeto de trabalho para verificar o status do trabalho. Ele aguardará até que o trabalho seja concluído ou o tempo de espera seja excedido

* **Get-AzureHDInsightJobOutput** - Usado para recuperar a saída do trabalho.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight. 

1. Usando um editor, salve o código a seguir como **pigjob.ps1**. Você deve substituir **CLUSTERNAME** com o nome do cluster HDInsight.

		#Login to your Azure subscription
		Add-AzureAccount

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

		# Print the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

2. Abra um novo prompt **PowerShell do Microsoft Azure**. Altere os diretórios para o local do arquivo **pigjob.ps1** e use o seguinte para executar o script.

		.\pigjob.ps1

7. Quando o trabalho for concluído, ele deverá retornar informações semelhantes à seguinte.

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            :
		PercentComplete : 100% complete
		Query           : LOGS = LOAD 'wasb:///example/data/sample.log';LEVELS = foreach LOGS generate REGEX_EXTRACT($0,
			'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is
			not null;GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;FREQUENCIES = foreach GROUPEDLEVELS
			generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;RESULT = order FREQUENCIES by
			COUNT desc;DUMP RESULT;
			State           : Completed
			StatusDirectory : /tutorial/pig/status
			SubmissionTime  : 11/20/2014 4:04:58 PM
			JobId           : job_1415949758166_0023

			Display the standard output ...
			(TRACE,816)
			(DEBUG,434)
			(INFO,96)
			(WARN,11)
			(ERROR,6)
			(FATAL,2)

##<a id="troubleshooting"></a>Solução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **pigjob.ps1**, salve e execute novamente.

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

Isso retornará as informações gravadas em STDERR no servidor ao executar o trabalho e pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos do Pig em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](../hdinsight-use-pig/)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](../hdinsight-use-hive/)

* [Usar o MapReduce com Hadoop no HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=47-->
