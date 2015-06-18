<properties
   pageTitle="Usar o Pig do Hadoop com .NET no HDInsight | Microsoft Azure"
   description="Aprenda a usar o SDK do .NET do Hadoop para enviar trabalhos do Pig para Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Executar trabalhos do Pig usando o SDK do .NET para Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de uso do SDK do .NET para Hadoop para enviar trabalhos do Pig para um Hadoop no cluster HDInsight.

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. O Pig permite que você crie operações MapReduce ao modelar uma série de transformações de dados. Você aprenderá como usar um aplicativo C# básico para enviar um trabalho do Pig para um cluster HDInsight.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (Windows ou Linux)

* Visual Studio 2012 ou 2013

##<a id="certificate"></a>Criar um certificado de gerenciamento

Para autenticar o aplicativo no Azure HDInsight, você deve criar um certificado autoassinado, instalá-lo em sua estação de trabalho de desenvolvimento e também carregá-lo em sua assinatura do Azure.

Para obter instruções sobre como fazer isso, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=511138" target="_blank">Criar um certificado autoassinado</a>.

> [AZURE.NOTE]Ao criar o certificado, certifique-se de observar o nome amigável usado, pois ele será usado posteriormente.

##<a id="subscriptionid"></a>Localizar sua ID de assinatura

Cada assinatura do Azure é identificada por um valor GUID, conhecido como a ID da assinatura. Use as etapas a seguir para encontrar esse valor.

1. Visite o <a href="https://manage.windowsazure.com/" target="_blank">Console de gerenciamento do Azure</a>.

2. Na barra à esquerda do portal, selecione **Configurações**.

3. Nas informações apresentadas à direita da página, localize a assinatura que deseja usar e observe o valor na coluna **ID da Assinatura**.

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

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo. Substitua o código existente pelo seguinte.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		
		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;
		
		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");
		
		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");
		
		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            Console.WriteLine("----- Press ENTER to continue.");
		            Console.ReadLine();
		        }
		
		        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
		        {
		            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
		            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
		            {
		                jobInProgress = client.GetJob(jobInProgress.JobId);
		                Thread.Sleep(TimeSpan.FromSeconds(10));
		            }
		        }
		
		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Salve o arquivo.

##<a id="run"></a>Execute o aplicativo.

Use **F5** para iniciar o aplicativo. Quando solicitado, insira a **ID da Assinatura**, o **Nome amigável do certificado** e o **Nome do cluster HDInsight**. O aplicativo gerará várias linhas de informação enquanto ele é executado, terminando com algo semelhante ao seguinte.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Pressione **ENTER** para sair do aplicativo.

##<a id="summary"></a>Resumo

Como você pode ver, o SDK do .NET para Hadoop permite criar aplicativos .NET que enviam trabalhos do Pig para um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54--> 