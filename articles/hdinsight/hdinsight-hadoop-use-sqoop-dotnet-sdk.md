---
title: Usar o Sqoop do Hadoop no HDInsight | Microsoft Docs
description: "Aprenda como usar o SDK .NET do HDInsight para executar a importação e a exportação do Sqoop entre um cluster do Hadoop e um banco de dados SQL do Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 027ac136d4c4f242c75e8a25605ace646624d64d


---
# <a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Executar trabalhos do Sqoop usando o SDK do .NET para Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK .NET do HDInsight para executar trabalhos do Sqoop no HDInsight a fim de importar e exportar entre um cluster HDInsight e um banco de dados SQL do Azure ou um banco de dados SQL Server.

> [!NOTE]
> As etapas neste artigo podem ser usadas com qualquer cluster HDInsight baseado em Linux ou Windows. No entanto, essas etapas só funcionarão em um cliente Windows. Use o seletor de tabulação na parte superior deste artigo para escolher outros métodos.
> 
> 

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Um cluster Hadoop no HDInsight**. Confira [Criar o cluster e o banco de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Executar Sqoop usando o SDK do .NET
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. Nesta seção, você criará um aplicativo de console em C# a fim de exportar o hivesampletable para a tabela Banco de Dados SQL criada por você nestes tutoriais.

**Para enviar um trabalho de Sqoop**

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
* Envio em lote — com HDInsight baseado em Linux, ao usar o comutador `-batch` na execução de inserções, Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Oozie com o HDInsight](hdinsight-use-oozie.md): use a ação do Sqoop no fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voos usando o HDInsight](hdinsight-analyze-flight-delay-data.md): use o Hive para analisar dados de atraso de voos e o Sqoop para exportar dados para o banco de dados SQL do Azure.
* [Carregar dados no HDInsight](hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.




<!--HONumber=Nov16_HO3-->


