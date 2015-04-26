<properties 
	pageTitle="Enviar trabalhos Hadoop no HDInsight | Azure" 
	description="Saiba como enviar trabalhos Hadoop para o Hadoop no Azure HDInsight." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>

# Enviar trabalhos Hadoop no HDInsight

Neste artigo, você aprenderá como enviar trabalhos MapReduce e Hive usando o PowerShell e o SDK do .NET HDInsight.

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].
* Instalar e configurar o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].


## Neste artigo

* [Enviar trabalhos MapReduce usando o PowerShell](#mapreduce-powershell)
* [Enviar trabalhos Hive usando o PowerShell](#hive-powershell)
* [Enviar trabalhos Sqoop usando o PowerShell](#sqoop-powershell)
* [Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight](#mapreduce-sdk)
* [Enviar trabalhos MapReduce de streaming do Hadoop usando o SDK do .NET do HDInsight](#streaming-sdk)
* [Enviar trabalhos Hive usando o SDK do .NET do HDInsight](#hive-sdk)
* [Próximas etapas](#nextsteps)

## <a id="mapreduce-powershell"></a> Enviar trabalhos MapReduce usando o PowerShell
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter mais informações sobre como usar o PowerShell com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].

O MapReduce do Hadoop é uma estrutura de software para escrever aplicativos que processam grandes quantidades de dados. Os clusters HDInsight são fornecidos com um arquivo jar, localizado em *\example\jars\hadoop-examples.jar*, que contém vários exemplos de MapReduce. Esse arquivo foi renomeado para hadoop-mapreduce-examples.jar em clusters HDInsight versão 3.0. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem. Nesta seção, você aprenderá como usar o PowerShell para executar o exemplo de contagem de palavras em uma estação de trabalho. Para obter mais informações sobre como desenvolver e executar trabalhos MapReduce, consulte [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce].

**Para executar o programa MapReduce de contagem de palavras usando o PowerShell**

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina essas duas variáveis executando os seguintes comandos do PowerShell:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	A assinatura é a que você usou para criar o cluster HDInsight. E o cluster HDInsight é aquele que você deseja usar para executar o trabalho MapReduce.
	
5. Execute os seguintes comandos para criar uma definição de trabalho MapReduce:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Existem dois argumentos. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. Para obter mais informações sobre o prefixo wasb, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

6. Execute o comando a seguir para executar o trabalho MapReduce:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce. 

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
2. Defina estas três variáveis executando os seguintes comandos do PowerShell:

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	A conta de Armazenamento do Azure é a que foi especificada durante a provisão do cluster HDInsight. A conta de armazenamento é usada para hospedar o contêiner de Blob usado como o sistema de arquivos de cluster HDInsight padrão.  Geralmente, o nome do contêiner de armazenamento de Blob compartilham o mesmo nome que o cluster HDInsight a menos que você especifique um nome diferente ao provisionar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	O Select-AzureSubscription é usado para definir a assinatura atual caso você tenha várias assinaturas, e a assinatura padrão não seja a que está em uso. 

4. Execute o comando a seguir para baixar a saída do trabalho de MapReduce do contêiner de Blob para a estação de trabalho:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta *example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho MapReduce. *part-r-00000* é o nome do arquivo padrão para a saída do trabalho MapReduce.  O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz do C.  O arquivo será baixado na pasta *C:\example\data\WordCountOutput\*.

5. Execute o comando a seguir para imprimir o arquivo de saída do trabalho de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	O trabalho MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens.  O script usa o comando findstr para listar todas as palavras que contêm "there".


> [AZURE.NOTE] Se você abrir o ./example/data/WordCountOutput/part-r-00000, uma saída de várias linhas de um trabalho MapReduce, no Bloco de Notas, observará que as quebras de linha não são renderizadas corretamente. Isso é esperado.









































































































































## <a id="hive-powershell"></a> Enviar trabalhos Hive usando o PowerShell
O Apache [hdinsight-use-hive][apache-hive] fornece um meio de executar o trabalho de MapReduce por meio de uma linguagem de script semelhante ao SQL, chamada *HiveQL*, que pode ser aplicada para resumo, consultas e análise de grandes volumes de dados. 

Os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Nesta seção, você usará o PowerShell para executar um trabalho Hive para listar alguns dados da tabela Hive. 

**Para executar um trabalho Hive usando o PowerShell**

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

2. Defina as duas primeiras variáveis nos comandos a seguir e, em seguida, execute os comandos:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	A $querystring é a consulta HiveQL.

3. Execute os seguintes comandos para selecionar a assinatura e o cluster do Azure para executar o trabalho Hive:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Envie o trabalho hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Você pode usar a opção -File para especificar um arquivo de script HiveQL no HDFS.

Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].

## <a id="sqoop-powershell"></a>Enviar trabalhos Sqoop usando o PowerShell

Consulte [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop].

## <a id="mapreduce-sdk"></a> Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. Os clusters HDInsight são fornecidos com um arquivo jar, localizado em *\example\jars\hadoop-examples.jar*, que contém vários exemplos de MapReduce. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem. Nesta seção, você aprenderá como criar um aplicativo .NET para executar o exemplo de contagem de palavras. Para obter mais informações sobre como desenvolver e executar trabalhos MapReduce, consulte [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce].


Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Criar um aplicativo de console
- Executar o aplicativo

**Para instalar o SDK do .NET do HDInsight**
Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2012.

2. No menu Arquivo, clique em **Novo** e **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:

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

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio. A versão deve ser 0.11.0.1 ou mais recente.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

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
	
10. Na função Main(), copie e cole o código a seguir:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
	
	Essas são todas as variáveis que você precisa definir para o programa. Você pode obter o nome da Assinatura do Azure no [Portal de Gerenciamento do Azure][azure-management-portal]. 

	Para obter informações sobre o certificado, consulte [Criar e baixar um certificado de gerenciamento para o Azure (a página pode estar em inglês)][azure-certificate]. Uma maneira fácil de configurar o certificado é executar os cmdlets *Get-AzurePublishSettingsFile* e *Import-AzurePublishSettingsFile* do PowerShell. Eles criarão e baixarão o certificado de gerenciamento automaticamente. Depois de executar os cmdlets do PowerShell, é possível abrir o *certmgr.msc* na estação de trabalho e localizar o certificado expandindo *Personal/Certificates*. O certificado criado pelos cmdlets do PowerShell tem *Azure Tools* para os campos *Issued To* e *Issued By*.

	O nome da conta de Armazenamento do Azure é o da conta que foi especificada durante a provisão do cluster HDInsight. O nome do contêiner padrão é igual ao nome do cluster HDInsight.
	
11. Na função Main(), acrescente o seguinte código para definir o trabalho MapReduce:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

   Existem dois argumentos. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. Para obter mais informações sobre o prefixo wasb, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].
		
12. Na função Main(), acrescente o seguinte código para criar um objeto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Na função Main(), acrescente o seguinte código para executar o trabalho e aguarde que o trabalho seja concluído:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Na função Main(), acrescente o seguinte código para imprimir a saída do trabalho MapReduce:

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

	A pasta de saída é especificada quando você define o trabalho MapReduce. O nome de arquivo padrão é *part-r-00000*.

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status e a saída do aplicativo. 

## <a id="streaming-sdk"></a> Enviar trabalhos de streaming do Hadoop usando o SDK do .NET do HDInsight
Os clusters HDInsight têm um programa de fluxo do Hadoop para contagem de palavras desenvolvido em C#. O programa mapeador é */example/apps/cat.exe* e o programa redutor é */example/apps/wc.exe*. Nesta seção, você aprenderá como criar um aplicativo .NET para executar o exemplo de contagem de palavras. 

Para obter os detalhes de como criar um aplicativo .Net para envio de trabalhos MapReduce, consulte [Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight](#mapreduce-sdk).

Para obter mais informações sobre o desenvolvimento e a implantação de trabalhos de streaming do Hadoop, consulte [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming-jobs].

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






## <a id="hive-sdk"></a> Enviar trabalhos Hive usando o SDK do .NET do HDInsight 
Os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Nesta seção, você criará um aplicativo .NET para executar um trabalho Hive para listar as tabelas Hive criadas no cluster HDInsight. Para obter mais informações sobre como usar o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**
Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2012.

2. No menu Arquivo, clique em **Novo** e **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.


5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes com o botão direito do mouse em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

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
	
10. Na função Main(), copie e cole o código a seguir:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
	
	Essas são todas as variáveis que você precisa definir para o programa. Você pode obter a ID da Assinatura do Azure com o administrador do sistema. 

	Para obter informações sobre o certificado, consulte [Criar e baixar um certificado de gerenciamento para o Azure (a página pode estar em inglês)][azure-certificate]. Uma maneira fácil de configurar o certificado é executar os cmdlets *Get-AzurePublishSettingsFile* e *Import-AzurePublishSettingsFile* do PowerShell. Eles criarão e baixarão o certificado de gerenciamento automaticamente. Depois de executar os cmdlets do PowerShell, é possível abrir o *certmgr.msc* na estação de trabalho e localizar o certificado expandindo *Personal/Certificates*. O certificado criado pelos cmdlets do PowerShell tem *Azure Tools* para os campos *Issued To* e *Issued By*.
	
11. Na função Main(), acrescente o seguinte código para definir o trabalho Hive:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	Você também pode usar o parâmetro File para especificar um arquivo de script HiveQL no HDFS. Por exemplo,

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. Na função Main(), acrescente o seguinte código para criar um objeto JobSubmissionCertificateCredential:
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Na função Main(), acrescente o seguinte código para executar o trabalho e aguarde que o trabalho seja concluído:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. Na função Main(), acrescente o seguinte código para imprimir a saída do trabalho Hive:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. A saída deve ser:

	hivesampletable




## <a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Provisionar clusters do HDInsight][hdinsight-provision]
* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Documentação de referência do cmdlet do HDInsight][hdinsight-powershell-reference]
* [Usar o hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png 
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!--HONumber=42-->
