<properties
   pageTitle="Usar o Pig do Hadoop com o PowerShell no HDInsight | Microsoft Azure"
   description="Saiba como enviar trabalhos do Pig para um cluster do Hadoop no HDInsight usando o PowerShell do Azure."
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

#Executar trabalhos do Pig usando o PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de uso do PowerShell do Azure para enviar trabalhos do Pig para um Hadoop no cluster HDInsight. O Pig permite que você escreva trabalhos do MapReduce usando uma linguagem (Pig Latin) que modela transformações de dados, em vez das funções mapear e reduzir.

> [AZURE.NOTE] Esse documento não fornece uma descrição detalhada do que fazem as instruções Pig Latin usadas nos exemplos. Para obter informações sobre o Pig Latin usado neste exemplo, consulte [Usar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Executar trabalhos do Pig usando o PowerShell

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos do Pig no HDInsight. Internamente, isso é feito por meio de chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os seguintes cmdlets são usados ao executar trabalhos do Pig em um cluster HDInsight remoto:

* **Login-AzureRmAccount**: autentica o Azure PowerShell para sua Assinatura do Azure

* **New-AzureRmHDInsightPigJobDefinition**: cria uma nova *definição de trabalho* usando as instruções de Pig Latin especificadas

* **Start-AzureRmHDInsightJob**: envia a definição do trabalho para o HDInsight, inicia o trabalho e retorna um objeto *job* que pode ser usado para verificar o status do trabalho

* **Wait-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguardará até que o trabalho seja concluído ou que o tempo de espera seja excedido.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **pigjob.ps1**. Você deve substituir **CLUSTERNAME** pelo nome do seu cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Abra um novo prompt de comando do Windows PowerShell. Altere os diretórios para o local do arquivo **pigjob.ps1** e use o seguinte comando para executar o script:

		.\pigjob.ps1
        
    Primeiro, você receberá uma solicitação para fazer logon em sua assinatura do Azure. Em seguida, receberá uma solicitação para fornecer o nome da conta e senha de Administrador/HTTPs para o cluster HDInsight.

7. Quando o trabalho for concluído, ele deverá retornar informações semelhantes às seguintes:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, um erro pode ter ocorrido durante o processamento. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **pigjob.ps1**, salve o arquivo e execute-o novamente.

	# Print the output of the Pig job.
	Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isso retornará as informações que foram gravadas em STDERR no servidor quando você executou o trabalho, e pode ajudar a determinar por que o trabalho está falhando.

##<a id="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos do Pig em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0622_2016-->