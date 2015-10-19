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
	ms.date="10/02/2015"
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

* **Um cluster Azure HDInsight**. Para obter instruções, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Criar clusters Hadoop no HDInsight][hdinsight-provision].
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

	O nome da conta de Armazenamento é a conta de armazenamento do Azure que foi especificada durante a criação do cluster HDInsight. A conta de armazenamento é usada para hospedar o contêiner de blob usado como o sistema de arquivos de cluster HDInsight padrão. Geralmente, o nome do contêiner compartilha o mesmo nome que o cluster HDInsight, a menos que você especifique um nome diferente ao criar o cluster.

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

**Para enviar o trabalho WordCount MapReduce**

1. No Visual Studio, crie um aplicativo de console C#.
2. No Console do Gerenciador de Pacotes NuGet, execute o comando a seguir.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Use as seguintes instruções using no arquivo Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Adicione o código a seguir à função Main().

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Adicione a função auxiliar a seguir à classe.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Pressione **F5** para executar o aplicativo.










##Enviar trabalhos de streaming do Hadoop usando o SDK do .NET do HDInsight
Os clusters HDInsight têm um programa de fluxo do Hadoop para contagem de palavras desenvolvido em C#. O programa mapeador é */example/apps/cat.exe* e o programa redutor é */example/apps/wc.exe*. Nesta seção, você aprenderá como criar um aplicativo .NET para executar o exemplo de contagem de palavras.

Para obter os detalhes de como criar um aplicativo .Net para envio de trabalhos de MapReduce, confira [Enviar trabalhos do MapReduce usando o SDK do .NET do HDInsight](#mapreduce-sdk).

Para saber mais sobre o desenvolvimento e a implantação de trabalhos de streaming do Hadoop, confira [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming-jobs].

O procedimento a seguir só funciona em clusters HDInsight no Windows. O streaming de C# ainda não tem suporte em clusters do Linux. No entanto, você pode usar o programa .NET para enviar o trabalho de fluxo escrito em outras linguagens de programação que são compatíveis com clusters do Linux. Por exemplo, Python. Para obter um exemplo de streaming para Python, consulte [Desenvolver programas de streaming Python para HDInsight](hdinsight-hadoop-streaming-python.md).

**Para enviar o trabalho WordCount MapReduce**

1. No Console do Gerenciador de Pacotes do Visual Studio, execute o seguinte comando do Nuget para importar o pacote.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Use as seguintes instruções using no arquivo Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Adicione o código a seguir à função Main().

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Adicione uma função de ajuda para a classe.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Pressione **F5** para executar o aplicativo.

##Enviar trabalhos do Hive usando o SDK do .NET do HDInsight
Os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Nesta seção, você criará um aplicativo .NET para executar um trabalho do Hive para listar as tabelas de Hive criadas no cluster HDInsight. Para saber mais sobre o Hive, confira [Usar o Hive com o HDInsight][hdinsight-use-hive].

Os seguintes procedimentos são necessários para criar um cluster HDInsight usando o SDK:

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
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Criar clusters Hadoop no HDInsight][hdinsight-provision]
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

<!---HONumber=Oct15_HO2-->