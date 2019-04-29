---
title: Use o MapReduce e o PowerShell com o Apache Hadoop - Azure HDInsight
description: Aprenda a usar o PowerShell para executar trabalhos do MapReduce remotamente com o Apache Hadoop no HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129018"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar tarefas do MapReduce com o Apache Hadoop no HDInsight usando o PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Esse documento fornece um exemplo de uso do PowerShell do Azure para executar um trabalho MapReduce em um Hadoop no cluster HDInsight.

## <a id="prereq"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Um cluster Azure HDInsight (Hadoop no HDInsight)**

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Uma estação de trabalho com o PowerShell do Azure.**

## <a id="powershell"></a>Executar um trabalho MapReduce

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos MapReduce no HDInsight. Internamente, o PowerShell faz chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar trabalhos MapReduce em um cluster HDInsight remoto.

* **Connect-AzAccount**: Autentica o Azure PowerShell para a assinatura do Azure.

* **New-AzHDInsightMapReduceJobDefinition**: Cria uma nova*definição de trabalho*, usando as informações do MapReduce especificadas.

* **Start-AzHDInsightJob**: Envia a definição de trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.

* **Wait-AzHDInsightJob**: Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.

* **Get-AzHDInsightJobOutput**: Usado para recuperar a saída do trabalho.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere os diretórios para o local do arquivo **mapreducejob.ps1** e use o seguinte comando para executar o script:

        .\mapreducejob.ps1

    Quando você executa o script, o nome do cluster HDInsight e o logon do cluster são solicitados. Você também poderá receber uma solicitação para autenticar a sua assinatura do Azure.

3. Quando o trabalho for concluído, você receberá uma saída semelhante ao seguinte texto:

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

    > [!NOTE]
    > Se o **ExitCode** for um valor diferente de 0, consulte [Solução de problemas](#troubleshooting).

    Este exemplo também armazena os arquivos baixados em um arquivo **output.txt** no diretório do qual você executa o script.

### <a name="view-output"></a>Exibir saída

Para ver as palavras e contagens produzidas pelo trabalho, abra o arquivo **output.txt** em um editor de texto.

> [!NOTE]
> Os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

## <a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os erros para o trabalho. Para exibir informações de erro para esse trabalho, adicione o seguinte comando ao final do arquivo **mapreducejob.ps1** , depois salve o arquivo e execute-o novamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações gravadas em STDERR conforme o trabalho é executado.

## <a id="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
