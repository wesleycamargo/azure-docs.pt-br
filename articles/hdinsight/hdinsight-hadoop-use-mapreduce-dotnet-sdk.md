---
title: Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight | Microsoft Docs
description: Saiba como enviar trabalhos MapReduce para o Hadoop do Azure HDInsight usando o SDK do .NET do HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 03f3898182d405bced8ad41b281e2ce2037b8343
ms.lasthandoff: 03/04/2017


---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar trabalhos MapReduce usando o SDK do .NET do HDInsight
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce usando o SDK do .NET do HDInsight. Os clusters HDInsight vêm com um arquivo jar com alguns exemplos de MapReduce. O arquivo jar está localizado em */example/jars/hadoop-mapreduce-examples.jar*.  Um dos exemplos é *wordcount*. Você desenvolve um aplicativo do console C# para enviar um trabalho wordcount.  O trabalho lê o arquivo */example/data/gutenberg/davinci.txt* e gera os resultados em */example/data/davinciwordcount*.  Se você desejar executar novamente o aplicativo, você deve limpar a pasta de saída.

> [!NOTE]
> As etapas neste artigo devem ser executadas em um cliente do Windows. Para obter informações sobre como usar um cliente Linux, OS X ou Unix para trabalhar com o Hive, use o seletor de tabulação mostrado na parte superior do artigo.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Um cluster Hadoop no HDInsight**. Confira a [Introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. 

**Para enviar trabalhos**

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes NuGet, execute o comando a seguir.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Use o seguinte código:
   
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
4. Pressione **F5** para executar o aplicativo.

Para executar o trabalho novamente, é necessário alterar o nome da pasta de saída do trabalho, no exemplo, "/example/data/davinciwordcount".

Quando o trabalho for concluído com êxito, a saída ficará em branco. Para ver o resultado do trabalho MapReduce, use o portal do Azure para explorar o contêiner de armazenamento padrão no armazenamento de Blobs.  O nome do arquivo é "part-r-00000".

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para enviar um trabalho de Hive, consulte [Executar consultas Hive usando o SDK do .NET HDInsight](hdinsight-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, confira [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters Hadoop baseados em Linux no HDInsight).
* Para gerenciar clusters HDInsight, consulte [Gerenciar clusters Hadoop no HDInsight](hdinsight-administer-use-portal-linux.md).
* Para aprender sobre o SDK do .NET do HDInsight, consulte [referência do SDK do .NET do HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Para autenticação não interativa no Azure, confira [Criar aplicativos .NET HDInsight de autenticação não interativa](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


