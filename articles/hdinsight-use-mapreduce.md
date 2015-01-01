<properties urlDisplayName="MapReduce with Hadoop in HDInsight" pageTitle="Usar o MapReduce do Hadoop no HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop MapReduce in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />



# Usar o MapReduce do Hadoop no HDInsight

O MapReduce do Hadoop é uma estrutura de software para gravar aplicativos que processam grandes quantidades de dados. Neste tutorial, você usará o PowerShell do Azure de sua estação de trabalho para enviar um programa MapReduce que conta as ocorrências de palavras em um texto para um cluster HDInsight. A contagem de palavras é escrita em Java, e o programa é fornecido com o cluster HDInsight.


**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

- Um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight][hdinsight-provision].

- Uma estação de trabalho com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

##Neste tutorial
1. [Compreender o cenário](#scenario)
2. [Executar o exemplo com o PowerShell do Azure](#run-sample)	
3. [O código Java do Programa MapReduce de contagem de palavras](#java-code)
4. [Próximas etapas](#next-steps)	

##<a id="scenario"></a>Compreender o cenário

O diagrama a seguir ilustra como o MapReduce funciona para o cenário de contagem de palavras:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]



A saída do trabalho MapReduce é um conjunto de pares de chave-valor. A chave é uma cadeia de caracteres que especifica uma palavra e o valor é um inteiro que especifica o número total de ocorrências daquela palavra no texto. Isso é feito em dois estágios: 

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Emite um par de chave/valor cada vez que ocorre um trabalho da palavra seguido por um 1. A saída será classificada antes de ser enviada ao redutor. 

* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

Executar um trabalho MapReduce requer os seguintes elementos:

* Um programa MapReduce. Neste tutorial, você usará o exemplo de contagem de palavras fornecido com os clusters HDInsight para que você não precise escrever seu próprio código. Ele está localizado em */example/jars/hadoop-examples.jar*. O nome do arquivo é*hadoop-mapreduce-examples.jar* em clusters HDInsight versão 3.0. Para obter instruções sobre como escrever o seu próprio trabalho MapReduce, consulte [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-MapReduce-jobs].
* Um arquivo de entrada. Você usará */example/data/gutenberg/davinci.txt* como o arquivo de entrada. Para obter informações sobre como carregar arquivos, consulte [Carregar dados para HDInsight][hdinsight-upload-data].
* Uma pasta do arquivo de saída. Você usará*/example/data/WordCountOutput* como a pasta do arquivo de saída. O sistema criará a pasta se ela não existir. O trabalho MapReduce falhará se a pasta existir.  Para executar o trabalho MapReduce pela segunda vez, exclua a pasta de saída ou especifique outra pasta de saída.

	
##<a id="run-sample"></a>Executar o exemplo com o PowerShell do Azure

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte[Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina as duas variáveis nos comandos a seguir e execute-as:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
4. Execute o comando a seguir e forneça suas informações de conta do Azure:

		Add-AzureAccount
		
5. Execute os seguintes comandos para criar uma definição de trabalho MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [WACOM.NOTE] *hadoop-examples.jar* é fornecido com os clusters do HDInsight versão 2.1. O arquivo foi renomeado como *hadoop-mapreduce.jar* em clusters do HDInsight versão 3.0.
	
	O arquivo hadoop-examples.jar é fornecido com a distribuição de cluster HDInsight. Existem dois argumentos para o trabalho de MapReduce. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. O arquivo de origem é fornecido com a distribuição de cluster HDInsight e o caminho do arquivo de saída será criado no tempo de execução.

6. Execute o comando a seguir para enviar o trabalho MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce e as credenciais. O Start-AzureHDInsightJob é uma chamada assincronizada. Para verificar a conclusão do trabalho, use o cmdlet *Wait-AzureHDInsightJob*.

7. Execute o seguinte comando para verificar a conclusão do trabalho de MapReduce:

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

8. Execute o seguinte comando para verificar qualquer erro com a execução do trabalho de MapReduce:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**Para recuperar os resultados do trabalho de MapReduce**

1. Abra o **PowerShell do Azure**.
2. Execute o comando a seguir para alterar o diretório para o C:\ raiz:

		cd \

	A pasta padrão do PowerShell do Azure é *C:\Windows\System32\WindowsPowerShell\v1.0*. Por padrão, você não tem permissão de gravação nessa pasta. Você deve alterar o diretório para o diretório C:\ raiz ou para uma pasta onde você tenha permissão de gravação.

2. Defina as três variáveis nos comandos a seguir e, em seguida, execute-as:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

		A Conta de Armazenamento do Azure é aquela que você criou anteriormente no tutorial. A conta de armazenamento é usada para hospedar o contêiner de Blob usado como o sistema de arquivos de cluster HDInsight padrão.  Geralmente, o nome do contêiner de armazenamento de Blob compartilham o mesmo nome que o cluster HDInsight a menos que você especifique um nome diferente ao provisionar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	O *Select-AzureSubscription* é usado para definir a assinatura atual caso você tenha várias assinaturas, e a assinatura padrão não seja a que está em uso. 

4. Execute o comando a seguir para baixar a saída do trabalho de MapReduce do contêiner de Blob para a estação de trabalho:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta */example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho MapReduce. *part-r-00000* é o nome do arquivo padrão de saída do trabalho de MapReduce.  O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz de C.  O arquivo será baixado na pasta *C:\example\data\WordCountOutput\* . 

5. Execute o comando a seguir para imprimir o arquivo de saída do trabalho de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	O trabalho de MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens.  O script usa o comando findstr para listar todas as palavras que contêm *"there"*.


Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

##<a id="java-code"></a>O código Java do Programa MapReduce de contagem de palavras

A seguir é apresentado o código-fonte do programa MapReduce de contagem de palavras:
 
	package org.apache.hadoop.examples;
	
	import java.io.IOException;
	import java.util.StringTokenizer;
	
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;
	
	public class WordCount {
	
	  public static class TokenizerMapper 
	       extends Mapper<Object, Text, Text, IntWritable>{
	    
	    private final static IntWritable one = new IntWritable(1);
	    private Text word = new Text();
	      
	    public void map(Object key, Text value, Context context
	                    ) throws IOException, InterruptedException {
	      StringTokenizer itr = new StringTokenizer(value.toString());
	      while (itr.hasMoreTokens()) {
	        word.set(itr.nextToken());
	        context.write(word, one);
	      }
	    }
	  }
	  
	  public static class IntSumReducer 
	       extends Reducer<Text,IntWritable,Text,IntWritable> {
	    private IntWritable result = new IntWritable();
	
	    public void reduce(Text key, Iterable<IntWritable> values, 
	                       Context context
	                       ) throws IOException, InterruptedException {
	      int sum = 0;
	      for (IntWritable val : values) {
	        sum += val.get();
	      }
	      result.set(sum);
	      context.write(key, result);
	    }
	  }
	
	  public static void main(String[] args) throws Exception {
	    Configuration conf = new Configuration();
	    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
	    if (otherArgs.length != 2) {
	      System.err.println("Usage: wordcount <in> <out>");
	      System.exit(2);
	    }
	    Job job = new Job(conf, "word count");
	    job.setJarByClass(WordCount.class);
	    job.setMapperClass(TokenizerMapper.class);
	    job.setCombinerClass(IntSumReducer.class);
	    job.setReducerClass(IntSumReducer.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(IntWritable.class);
	    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
	    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
	    System.exit(job.waitForCompletion(true) ? 0 : 1);
	  }
	}
 


##<a id="nextsteps"></a>Próximas etapas
Embora o MapReduce forneça recursos avançados para diagnóstico, ele pode ser um pouco difícil de dominar. Outras linguagens, como Pig e Hive, fornecem uma maneira fácil de trabalhar com dados armazenados no HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-MapReduce-jobs]
* [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig] 
* [Executar os exemplos do HDInsight][hdinsight-samples]


[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-mapreduce-jobs]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[powershell-install-configure]: ../install-and-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif






<!--HONumber=35_1-->
