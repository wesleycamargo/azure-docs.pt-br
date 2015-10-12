<properties
	pageTitle="Enviar trabalhos do Hadoop no HDInsight | Microsoft Azure"
	description="Saiba como enviar trabalhos Hadoop para o Hadoop no Azure HDInsight."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="jgao"/>

# Enviar trabalhos Hadoop no HDInsight

Aprenda a usar o PowerShell do Azure para enviar trabalhos do MapReduce e do Hive e a usar o SDK de .NET do HDInsight para enviar trabalhos do MapReduce, do Hive e streaming do Hadoop.

> [AZURE.NOTE]As etapas neste artigo devem ser executadas em um cliente do Windows. Para obter informações sobre como usar um cliente Linux, OS X ou Unix para trabalhar com MapReduce, Hive ou Pig no HDInsight, consulte os seguintes artigos e selecione os links **SSH** ou **Curl** dentro de cada uma:
>
> - [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
> - [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
> - [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

* **Um cluster Azure HDInsight**. Para obter instruções, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].
- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Enviar trabalhos do MapReduce usando o PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para saber mais sobre como usar o PowerShell com o HDInsight, confira [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

O MapReduce do Hadoop é uma estrutura de software para escrever aplicativos que processam grandes quantidades de dados. Os clusters HDInsight são fornecidos com um arquivo JAR (localizado em *\\example\\jars\\hadoop-examples.jar*), que contém vários exemplos de MapReduce.

Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem. Nesta seção, você aprenderá a usar o PowerShell do Azure de uma estação de trabalho para executar um exemplo de contagem de palavras. Para saber mais sobre como desenvolver e executar trabalhos do MapReduce, confira [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce].

**Para executar o programa MapReduce de contagem de palavras usando o PowerShell do Azure**

1.	Abra o **PowerShell do Azure**. Para saber mais sobre como abrir a janela do console do PowerShell do Azure, confira [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina as variáveis abaixo executando os seguintes comandos do PowerShell do Azure:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	O nome de assinatura é o que você usou para criar o cluster HDInsight. O cluster HDInsight é aquele que você deseja usar para executar o trabalho do MapReduce.

5. Execute os seguintes comandos para criar uma definição de trabalho MapReduce:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Existem dois argumentos. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. Para saber mais sobre o prefixo wasb://, confira [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

6. Execute o comando a seguir para executar o trabalho MapReduce:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	Além da definição do trabalho do MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho do MapReduce.

7. Execute o seguinte comando para verificar a conclusão do trabalho de MapReduce:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600


8. Execute o seguinte comando para verificar qualquer erro com a execução do trabalho de MapReduce:

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

	A captura de tela a seguir mostra a saída de uma execução bem sucedida. Caso contrário, você verá algumas mensagens de erro.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]


**Para recuperar os resultados do trabalho de MapReduce**

1. Abra o **PowerShell do Azure**.
2. Defina as variáveis abaixo executando os seguintes comandos do PowerShell do Azure:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	A conta de Armazenamento do Azure é a que foi especificada durante a provisão do cluster HDInsight. A conta de armazenamento é usada para hospedar o contêiner de blob usado como o sistema de arquivos de cluster HDInsight padrão. Geralmente, o nome do contêiner compartilha o mesmo nome que o cluster HDInsight, a menos que você especifique um nome diferente ao configurar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento de blob do Azure:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	O **Select-AzureSubscription** é usado para definir a assinatura atual caso você tenha várias assinaturas e a assinatura padrão não for a selecionada.

4. Execute o seguinte comando para baixar a saída do trabalho de MapReduce do contêiner de blob para a estação de trabalho:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta *example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho de MapReduce. *part-r-00000* é o nome de arquivo padrão para a saída do trabalho de MapReduce. O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na captura de tela a seguir, a pasta atual é a pasta raiz C:\\. O arquivo será baixado para:

**C:\\example\\data\\WordCountOutput*

5. Execute o comando a seguir para imprimir a saída do trabalho de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	O trabalho de MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens. O script usa o comando **findstr** para listar todas as palavras que contêm "there".


> [AZURE.NOTE]Se você abrir o ./example/data/WordCountOutput/part-r-00000 (uma saída de várias linhas de um trabalho MapReduc), no Bloco de Notas, observará que as quebras de linha não são renderizadas corretamente. Isso é esperado.









































































































































##Enviar trabalhos do Hive usando o PowerShell do Azure
O [Apache Hive][apache-hive] fornece um meio de executar o trabalho do MapReduce por meio de uma linguagem de script semelhante ao SQL, chamada *HiveQL*, que pode ser aplicada para sumarização, consultas e análise de grandes volumes de dados.

Os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Nesta seção, você usará o PowerShell do Azure para executar um trabalho do Hive para listar alguns dados da tabela do Hive.

**Para executar um trabalho do Hive usando o PowerShell do Azure**

1.	Abra o **PowerShell do Azure**. Para saber mais sobre como abrir a janela do console do PowerShell do Azure, confira [Instalar e configurar o PowerShell do Azure￼][powershell-install-configure].

2. Defina as duas primeiras variáveis nos comandos a seguir e, em seguida, execute os comandos:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	A $querystring é a consulta HiveQL.

3. Execute os seguintes comandos para selecionar a assinatura do Azure e o cluster para executar o trabalho do Hive:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Execute o seguinte script para enviar o trabalho do Hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Você pode usar a opção **-File** para especificar um arquivo de script HiveQL no sistema de arquivos distribuído do Hadoop (HDFS).

Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].


## Enviar trabalhos do Hive usando o Visual Studio

Confira [Introdução às Ferramentas do Hadoop do HDInsight para Visual Studio][hdinsight-visual-studio-tools].

##Enviar trabalhos do Sqoop usando o PowerShell do Azure

Consulte [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop].

##Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight do .NET. Os clusters HDInsight são fornecidos com um arquivo JAR (localizado em *\\example\\jars\\hadoop-examples.jar*), que contém vários exemplos de MapReduce. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem. Nesta seção, você aprenderá como criar um aplicativo .NET para executar o exemplo de contagem de palavras. Para saber mais sobre como desenvolver e executar trabalhos MapReduce, confira [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce].


Os seguintes procedimentos são necessários para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight** Você pode instalar a compilação do SDK publicada mais recentemente no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicativo de console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. Clique em **OK** para criar o projeto.


5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio. A versão deve ser 0.11.0.1 ou posterior.

7. No **Gerenciador de Soluções**, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Adicione a seguinte definição de função à classe. Essa função é usada para aguardar a conclusão do trabalho Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Na função **Main()**, cole o seguinte código:

		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";

		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";


	Essas são todas as variáveis que você precisa definir para o programa. Você pode obter o nome da assinatura do Azure no [portal de visualização do Azure][azure-management-portal].

	Para saber mais sobre o certificado, confira [Criar e baixar um certificado de gerenciamento para o Azure][azure-certificate]. Uma maneira fácil de configurar o certificado é executar os cmdlets **Get-AzurePublishSettingsFile** e **Import-AzurePublishSettingsFile** do PowerShell do Azure. Eles criarão e baixarão o certificado de gerenciamento automaticamente. Depois de executar os cmdlets do PowerShell, você pode abrir o **certmgr.msc** na estação de trabalho e localizar o certificado expandindo **Personal** > **Certificates**. O certificado criado pelos cmdlets do PowerShell do Azure tem Ferramentas do Azure para os campos **Emitido para** e **Emitido por**.

	O nome da conta de Armazenamento do Azure é o da conta que foi especificada durante a provisão do cluster HDInsight. O nome do contêiner padrão é igual ao nome do cluster HDInsight.

11. Na função **Main()**, acrescente o seguinte código para definir o trabalho do MapReduce:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Existem dois argumentos. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. Para saber mais sobre o prefixo wasb://, confira [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

12. Na função **Main()**, acrescente o seguinte código para criar um objeto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Na função **Main()**, acrescente o seguinte código para executar o trabalho e aguarde que o trabalho seja concluído:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Na função **Main()**, acrescente o seguinte código para imprimir a saída do trabalho do MapReduce:

		// Print the MapReduce job output
		Stream stream = new MemoryStream();

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");

		blockBlob.DownloadToStream(stream);
		stream.Position = 0;

		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	A pasta de saída é especificada quando você define o trabalho MapReduce. O nome do arquivo padrão é **part-r-00000**.

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status e a saída do aplicativo.

##Enviar trabalhos de streaming do Hadoop usando o SDK do .NET do HDInsight
Os clusters HDInsight têm um programa de fluxo do Hadoop para contagem de palavras desenvolvido em C#. O programa mapeador é */example/apps/cat.exe* e o programa redutor é */example/apps/wc.exe*. Nesta seção, você aprenderá como criar um aplicativo .NET para executar o exemplo de contagem de palavras.

Para obter os detalhes de como criar um aplicativo .Net para envio de trabalhos de MapReduce, confira [Enviar trabalhos do MapReduce usando o SDK do .NET do HDInsight](#mapreduce-sdk).

Para saber mais sobre o desenvolvimento e a implantação de trabalhos de streaming do Hadoop, confira [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming-jobs].

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

	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";

				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };

	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);

	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);

	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);

	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }

	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
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
	    }
	}






##Enviar trabalhos do Hive usando o SDK do .NET do HDInsight
Os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Nesta seção, você criará um aplicativo .NET para executar um trabalho do Hive para listar as tabelas de Hive criadas no cluster HDInsight. Para saber mais sobre o Hive, confira [Usar o Hive com o HDInsight][hdinsight-use-hive].

Os seguintes procedimentos são necessários para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight** Você pode instalar a compilação do SDK publicada mais recentemente no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2013 ou 2015.

2. Crie um projeto novo com as seguintes configurações:

	|Propriedade|Valor|
	|--------|-----|
	|Modelo|Templates/Visual C#/Windows/Console Application|
	|Nome|SubmitHiveJob|

3. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.
4. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Esses comandos adicionam bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

5. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo, cole o seguinte código e forneça valores para as variáveis:

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Pressione **F5** para executar o aplicativo.

##Enviar trabalhos usando as ferramentas do HDInsight para Visual Studio

Usando as Ferramentas do HDInsight para Visual Studio, você poderá executar consultas Hive e scripts Pig. Confira [Introdução às ferramentas do Hadoop do Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Provisionar clusters HDInsight][hdinsight-provision]
* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Documentação de referência do cmdlet do HDInsight][hdinsight-powershell-reference]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO1-->