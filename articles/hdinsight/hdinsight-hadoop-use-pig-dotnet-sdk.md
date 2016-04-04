<properties
   pageTitle="Usar o Pig do Hadoop com .NET no HDInsight | Microsoft Azure"
   description="Aprenda a usar o SDK do .NET do Hadoop para enviar trabalhos do Pig para Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Executar trabalhos do Pig usando o SDK do .NET para Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de uso do SDK do .NET para Hadoop para enviar trabalhos do Pig para um Hadoop no cluster HDInsight.

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. O Pig permite que você crie operações MapReduce ao modelar uma série de transformações de dados. Você aprenderá como usar um aplicativo C# básico para enviar um trabalho do Pig para um cluster HDInsight.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Executar trabalhos do Pig usando o SDK do .NET para Hadoop no HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (Windows ou Linux)

* Visual Studio 2012 ou 2013 ou 2015.

##<a id="certificate"></a>Criar um certificado de gerenciamento

Para autenticar o aplicativo no Azure HDInsight, você deve criar um certificado autoassinado, instalá-lo em sua estação de trabalho de desenvolvimento e também carregá-lo em sua assinatura do Azure.

Para obter instruções sobre como fazer isso, confira [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE] Ao criar o certificado, certifique-se de observar o nome amigável usado, pois ele será usado posteriormente.

##<a id="subscriptionid"></a>Localizar sua ID de assinatura

Cada assinatura do Azure é identificada por um valor GUID, conhecido como a ID da assinatura. Use as etapas a seguir para encontrar esse valor.

1. Visite o [Portal do Azure][preview-portal].

2. Na barra à esquerda do portal, selecione __PROCURAR TUDO__ e selecione __Assinaturas__ na folha __Procurar__.

3. Nas informações apresentadas na folha __Assinaturas__, localize a assinatura que deseja usar e observe o valor na coluna **ID da Assinatura**.

Salve a ID da assinatura, que será usada mais tarde.

##<a id="create"></a>Criar o aplicativo

1. Abrir o Visual Studio 2012 ou 2013
2. No menu **Arquivo**, selecione **Novo** e **Projeto**.
3. Para o novo projeto, digite ou selecione os valores a seguir.

	<table>
	<tr>
	<th>Propriedade</th>
	<th>Valor</th>
	</tr>
	<tr>
	<th>Categoria</th>
	<th>Modelos/Visual C#/Windows</th>
	</tr>
	<tr>
	<th>Modelo</th>
	<th>Aplicativo de console</th>
	</tr>
	<tr>
	<th>Nome</th>
	<th>SubmitPigJob</th>
	</tr>
	</table>
4. Clique em **OK** para criar o projeto.
5. No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** ou **Gerenciador de Pacotes NuGet** e depois selecione **Console do Gerenciador de Pacotes**.
6. Execute o seguinte comando no console para instalar os pacotes do SDK do .NET.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo. Substitua o código existente pelo seguinte.

        using System;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace HDInsightSubmitPigJobsDotNet
        {
            class Program
            {
                static void Main(string[] args)
                {
                    var ExistingClusterName = "<HDInsightClusterName>";
                    var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                    var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
                    var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
        
                    // The Pig Latin statements to run
                    string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;";
        
        
                    HDInsightJobManagementClient _hdiJobManagementClient;
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    // Define the Pig job
                    var parameters = new PigJobSubmissionParameters()
                    {
                        Query = queryString,
                    };
        
                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                    Console.WriteLine("Press ENTER to continue ...");
                    Console.ReadLine();
                }
            }
        }

7. Pressione **F5** para iniciar o aplicativo.
8. Pressione **ENTER** para sair do aplicativo.

##<a id="summary"></a>Resumo

Como você pode ver, o SDK do .NET para Hadoop permite criar aplicativos .NET que enviam trabalhos do Pig para um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0323_2016-->