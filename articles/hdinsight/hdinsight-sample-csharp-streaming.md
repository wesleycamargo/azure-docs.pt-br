<properties
	pageTitle="Amostra Hadoop de contagem de palavras de streaming em C# | Microsoft Azure"
	description="Como escrever programas MapReduce em C# que utilizem a interface de streaming do Hadoop e como executar os programas no HDInsight usando cmdlets do PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# A amostra de contagem de palavras de streaming C# MapReduce no Hadoop no HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapa e de redução em outras linguagens além do Java. Este tutorial mostra como escrever programas MapReduce em C# usando a interface de Streaming do Hadoop e como executar os programas no Azure HDInsight usando cmdlets do PowerShell do Azure.

> [AZURE.NOTE]As etapas deste tutorial se aplicam apenas aos clusters HDInsight baseados no Windows. Para obter um exemplo de streaming para clusters do HDInsight baseados em Linux, consulte [Desenvolver programas de streaming em Python para o HDInsight](hdinsight-hadoop-streaming-python.md).

No exemplo, o mapeador e o redutor são executáveis que leem a entrada de [stdin][stdin-stdout-stderr] (linha por linha) e emitem a saída para [stdout][stdin-stdout-stderr]. O programa conta todas as palavras do texto.

Quando um executável é especificado para **mappers**, cada tarefa do mapeador inicia o executável como um processo separado quando o mapeador é inicializado. À medida que a tarefa do mapeador é executada, converte suas entradas em linhas e alimenta as linhas para o [stdin][stdin-stdout-stderr] do processo.

Enquanto isso, o mapeador coleta a saída orientada a linha do stdout do processo. Ele converte cada linha em um par chave/valor, que é coletado como a saída do mapeador. Por padrão, o prefixo de uma linha até o primeiro caractere de tabulação é a chave, e o restante da linha (exceto o caractere de tabulação) é o valor. Se não houver nenhum caractere de tabulação na linha, toda a linha será considerada como chave e o valor será nulo.

Quando um executável é especificado para **reducers**, cada tarefa do redutor inicia o executável como um processo separado quando o redutor é inicializado. À medida que a tarefa do redutor é executada, converte seus pares de chave/valor de entrada em linhas e alimenta as linhas para o [stdin][stdin-stdout-stderr] do processo.

Enquanto isso, o redutor coleta a saída orientada a linha de [stdout][stdin-stdout-stderr] do processo. Ele converte cada linha em um par chave/valor, que é coletado como a saída do redutor. Por padrão, o prefixo de uma linha até o primeiro caractere de tabulação é a chave, e o restante da linha (exceto o caractere de tabulação) é o valor.

Para obter mais informações sobre a interface Streaming do Hadoop, consulte [Streaming do Hadoop][hadoop-streaming].

**Neste tutorial, você aprenderá a:**

* Usar o PowerShell do Azure para executar um programa de streaming em C# para analisar os dados contidos em um arquivo no HDInsight.
* Escrever código em C# que utilize a interface Streaming do Hadoop.


**Pré-requisitos**:

Antes de começar, você deverá ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **um cluster do HDInsight**. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [[Provisionar clusters HDInsight](hdinsight-provision-clusters.md).
- **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Executar a amostra com o PowerShell do Azure

**Para executar o trabalho MapReduce**

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina as duas variáveis nos comandos a seguir e execute-as:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Execute o comando a seguir para definir o trabalho MapReduce:

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	Os parâmetros especificam as funções do mapeador e do redutor e os arquivos de entrada e de saída.

5. Execute os comandos a seguir para executar o trabalho MapReduce, aguarde a conclusão do trabalho e imprima a mensagem de erro padrão:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. Execute os comandos a seguir para exibir os resultados da contagem de palavras:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esta amostra novamente, será necessário alterar o nome do arquivo de saída.


## <a id="java-code"></a>O código C# para o Streaming do Hadoop


O programa MapReduce usa o aplicativo cat.exe como uma interface de mapeamento para transmitir o texto para o console e o aplicativo wc.exe como a interface de redução para contar o número de palavras transmitidas de um documento. O mapeador e o redutor leem caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravam o fluxo da saída padrão (stdout).



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



O código do mapeador no arquivo cat.cs usa um objeto [StreamReader][streamreader] para ler os caracteres do fluxo de entrada no console que, por sua vez, grava o fluxo no fluxo de saída padrão com o método estático [Console.Writeline][console-writeline].


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


O código do redutor no arquivo wc.cs usa um objeto [StreamReader][streamreader] para ler os caracteres do fluxo de entrada padrão que produziu a saída do mapeador cat.exe. À medida que lê os caracteres com o método [console.WriteLine][console-writeline], ele conta as palavras ao contar espaços e caracteres de final de linha no final de cada palavra. Em seguida, grava o total no fluxo de saída padrão com o método [Console.WriteLine][console-writeline].


## <a id="summary"></a>Resumo

Neste tutorial, você viu como implantar um trabalho MapReduce no HDInsight usando o Streaming do Hadoop.

## <a id="next-steps"></a>Próximas etapas


Para obter tutoriais que descrevam outros exemplos e que forneçam instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Amostra: Estimador de pi][hdinsight-sample-pi-estimator]
* [Amostra: contagem de palavras][hdinsight-sample-wordcount]
* [Amostra: graySort de 10GB][hdinsight-sample-10gb-graysort]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=August15_HO6-->