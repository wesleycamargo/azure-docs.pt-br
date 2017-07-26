---
title: "Executar trabalhos Sqoop usando o .NET e o HDInsight – Azure | Microsoft Docs"
description: "Aprenda como usar o SDK .NET do HDInsight para executar a importação e a exportação do Sqoop entre um cluster do Hadoop e um banco de dados SQL do Azure."
keywords: trabalho de sqoop
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: c95641fc6d20e2911e007d1974b9e2c2398b3133
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---
# <a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Executar trabalhos do Sqoop usando o SDK do .NET para Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK .NET do HDInsight para executar trabalhos do Sqoop no HDInsight a fim de importar e exportar entre um cluster HDInsight e um banco de dados SQL do Azure ou um banco de dados SQL Server.

> [!NOTE]
> As etapas neste artigo podem ser usadas com qualquer cluster HDInsight baseado em Linux ou Windows. No entanto, essas etapas só funcionam em um cliente Windows. Use o seletor de tabulação na parte superior deste artigo para escolher outros métodos.
> 
> 

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes itens:

* **Um cluster Hadoop no HDInsight**. Confira [Criar o cluster e o banco de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-using-net-sdk"></a>Usar o Sqoop em clusters HDInsight usando o SDK do .NET
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. Nesta seção, você cria um aplicativo de console em C# a fim de exportar o hivesampletable para a tabela Banco de Dados SQL criada por você neste tutorial.

## <a name="submit-a-sqoop-job"></a>Enviar um trabalho de Sqoop

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes do Visual Studio, execute o seguinte comando do Nuget para importar o pacote.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Use o seguinte código no arquivo Program.cs:
   
        using System.Collections.Generic;
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
4. Pressione **F5** para executar o programa. 

## <a name="limitations"></a>Limitações
* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.
* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop realizará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Oozie com o HDInsight](hdinsight-use-oozie.md): use a ação do Sqoop no fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voos usando o HDInsight](hdinsight-analyze-flight-delay-data.md): use o Hive para analisar dados de atraso de voos e o Sqoop para exportar dados para o banco de dados SQL do Azure.
* [Carregar dados no HDInsight](hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.


