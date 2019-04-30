---
title: Executar trabalhos de Pig do Apache com o SDK do .NET para Hadoop – Azure HDInsight
description: Aprenda a usar o SDK do .NET do Hadoop para enviar trabalhos do Pig para Hadoop no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: ebf1f2806a6606294c61860a24fb2f02033a4bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110949"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar tarefas do Apache Pig usando o SDK do .NET para o Apache Hadoop no HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como usar o SDK do .NET para o Apache Hadoop para enviar trabalhos do Pig Apache para Hadoop no HDInsight do Azure.

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. O Pig permite que você crie operações MapReduce ao modelar uma série de transformações de dados. Neste documento, você aprenderá como usar um aplicativo C# básico para enviar um trabalho do Pig para um cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisa do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (Windows ou Linux).

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio 2012, 2013, 2015 ou 2017.

## <a name="create-the-application"></a>Criar o aplicativo

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET.

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** e **Projeto**.

2. Para o novo projeto, digite ou selecione os valores a seguir:

   | Propriedade | Value |
   | ------ | ------ |
   | Category | Modelos/Visual C#/Windows |
   | Modelo | Aplicativo de console |
   | NOME | SubmitPigJob |

3. Clique em **OK** para criar o projeto.

4. No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** ou **Gerenciador de Pacotes NuGet** e depois selecione **Console do Gerenciador de Pacotes**.

5. Execute o seguinte comando para instalar os pacotes do SDK do .NET:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo. Substitua o código existente pelo seguinte.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Pressione **F5** para iniciar o aplicativo.

8. Pressione **ENTER** para sair do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o Pig no HDInsight, consulte [Usar o Apache Pig com Apache Hadoop no HDInsight](hdinsight-use-pig.md).

Para saber mais sobre como usar o Hadoop no HDInsight, veja os seguintes documentos:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
