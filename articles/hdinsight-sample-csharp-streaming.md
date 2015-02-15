<properties 
	pageTitle="Amostra de Wordcount Hadoop de streaming em C# no HDInsight | Azure" 
	description="Como escrever programas MapReduce em C# que usam a interface de streaming do Hadoop e como executar os programas no HDInsight usando cmdlets do PowerShell." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# A amostra de MapReduce WordCount de streaming C# em Hadoop no HDInsight 
 
O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapa e redução em outras linguagens além do Java. Este tutorial mostra como escrever programas MapReduce em C# que usam a interface de streaming do Hadoop e como executar os programas no Azure HDInsight usando cmdlets do PowerShell do Azure. 

No exemplo, o mapeador e o redutor são executáveis que leem a entrada de [stdin][stdin-stdout-stderr] (linha por linha) e emitem a saída para [stdout][stdin-stdout-stderr]. O programa conta todas as palavras do texto.

Quando um executável é especificado para **mappers**, cada tarefa do mapeador inicia o executável como um processo separado quando o mapeador é inicializado. À medida que a tarefa do mapeador é executada, ele converte suas entradas em linhas e alimenta as linhas para o [stdin][stdin-stdout-stderr] do processo. Enquanto isso, o mapeador coleta as saídas orientadas a linhas do stdout do processo e converte cada linha em um par de chave/valor, que é coletado como a saída do mapeador. Por padrão, o prefixo de uma linha até o primeiro caractere de tabulação é a chave, e o restante da linha (exceto o caractere de tabulação) é o valor. Se não houver nenhum caractere de tabulação na linha, toda a linha será considerada como chave e o valor será nulo. 

Quando um executável é especificado para **reducers**, cada tarefa do redutor inicia o executável como um processo separado quando o redutor é inicializado. À medida que a tarefa do redutor é executada, ele converte seus pares de chave/valor de entrada em linhas e alimenta as linhas para o [stdin][stdin-stdout-stderr] do processo. Enquanto isso, o redutor coleta as saídas orientadas a linhas do [stdout][stdin-stdout-stderr] do processo e converte cada linha em um par de chave/valor, que é coletado como a saída do redutor. Por padrão, o prefixo de uma linha até o primeiro caractere de tabulação é a chave, e o restante da linha (exceto o caractere de tabulação) é o valor. 

Para obter mais informações sobre a interface de streaming do Hadoop, consulte [Streaming do Hadoop][hadoop-streaming]. 
 
**Você aprenderá:**	
	
* Como usar o PowerShell do Azure para executar um programa de streaming em C# para analisar os dados contidos em um arquivo no HDInsight.		
* Como escrever código em C# que usa a interface de Streaming do Hadoop.


**Pré-requisitos**:	

- Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)](http://azure.microsoft.com/pt-br/pricing/free-trial/).

- Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight](../hdinsight-provision-clusters/)

- Você deve ter instalado o PowerShell do Azure e o configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].


## Neste artigo
Este tópico mostra como executar o exemplo, apresenta o código Java do programa MapReduce, resume o que você aprendeu e descreve algumas das próximas etapas. Contém as seguintes seções.
	
1. [Executar o exemplo com o PowerShell do Azure](#run-sample)	
2. [O código C# para o streaming do Hadoop](#java-code)
3. [Resumo](#summary)	
4. [Próximas etapas](#next-steps)	

<h2><a id="run-sample"></a>Executar o exemplo com o PowerShell do Azure</h2>

**Para executar o trabalho MapReduce**

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

2. Defina as duas variáveis nos comandos a seguir e execute-as:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


3. Execute o comando a seguir para definir o trabalho MapReduce.
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	Os parâmetros especificam as funções do mapeador e do redutor e os arquivos de entrada e de saída.
                 
4. Execute os seguintes comandos para executar o trabalho MapReduce, aguarde a conclusão do trabalho e imprima o erro padrão:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5. Execute os seguintes comandos para exibir os resultados da contagem de palavras.

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.
	
<h2><a id="java-code"></a>O código C# para o streaming do Hadoop</h2>

O programa MapReduce usa o aplicativo cat.exe como uma interface de mapeamento para transmitir o texto para o console e o aplicativo wc.exe como a interface de redução para contar o número de palavras que são transmitidas de um documento. O mapeador e o redutor leem caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravam o fluxo de saída padrão (stdout). 



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

 

O código do mapeador no arquivo cat.cs usa um objeto StreamReader para ler os caracteres do fluxo de entrada no console que, por sua vez, grava o fluxo no fluxo de saída padrão com o método estático Console.Writeline.


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


O código do redutor no arquivo wc.cs usa um objeto [StreamReader][streamreader]   para ler os caracteres do fluxo de entrada padrão que produziu a saída do mapeador cat.exe. À medida que lê os caracteres com o método [Console.Writeline][console-writeline], ele conta as palavras contando caracteres de espaços e de fim de linha no final de cada palavra e, em seguida, grava o total no fluxo de saída padrão com o método [Console.Writeline][console-writeline]. 

<h2><a id="summary"></a>Resumo</h2>

Neste tutorial, você viu como implantar um trabalho MapReduce no HDInsight usando o Streaming do Hadoop.

<h2><a id="next-steps"></a>Próximas etapas</h2>

Para obter tutoriais que executam outros exemplos e fornecem instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Exemplo: Vistoriador de Pi][hdinsight-sample-pi-estimator]
* [Exemplo: Contagem de palavras][hdinsight-sample-wordcount][
* [Exemplo: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o hive com o HDInsight][hdinsight-use-hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/pt-br/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/pt-br/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/pt-br/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/



<!--HONumber=42-->
