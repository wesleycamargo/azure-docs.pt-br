<properties
	pageTitle="Exemplo de contagem de palavras do MapReduce do Hadoop no HDInsight | Microsoft Azure"
	description="Execute um exemplo de contagem de palavras do MapReduce em um cluster do Hadoop no HDInsight O programa, escrito em Java, contagens de ocorrências de palavras em um arquivo de texto."
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
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Executar programa contador de palavra MapReduce no cluster Hadoop no HDInsight

Saiba como executar um programa MapReduce no cluster Hadoop no HDInsight usando o Azure PowerShell. O programa é escrito em Java, conta as ocorrências de palavras em um arquivo de texto e então produz um novo arquivo de texto contendo cada palavra emparelhada com a sua frequência de ocorrência.

O programa é instalado nos clusters. O arquivo de texto analisado neste tutorial é a edição em eBook do Project Gutenberg de The Notebooks of Leonardo Da Vinci.

> [AZURE.NOTE]As etapas deste documento exigem um cliente do Windows. Para obter etapas sobre como usar o exemplo de contagem de palavras em um cliente Linux, OS X ou Unix, com um cluster do HDInsight para Linux, consulte [Usar o MapReduce com o Hadoop no HDInsight com SSH](hdinsight-hadoop-use-mapreduce-ssh.md) ou [Usar o MapReduce com Hadoop no HDInsight usando Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Outros artigos relacionados:**

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Desenvolver programas Java MapReduce para Hadoop no HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Enviar trabalhos Hadoop no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Amostra: graySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Amostra: estimador de pi][hdinsight-sample-pi-estimator]
* [Amostra: streaming em C#][hdinsight-sample-cs-streaming]

**Pré-requisitos**:

- **Um cluster HDInsight**. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight](hdinsight-provision-clusters.md).
- **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Executar o exemplo usando o Azure PowerShell

**Para enviar o trabalho MapReduce**

1. Abra o **ISE do Windows PowerShell**. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Cole o seguinte script do PowerShell.

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Defina as três primeiras variáveis e execute o script.
		
**Para recuperar os resultados do trabalho de MapReduce**

1. Abra o **ISE do Windows PowerShell**. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Cole o seguinte script do PowerShell.

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	O trabalho MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens. O script usa o comando **findstr** para listar todas as palavras que contêm *"there"*.

A saída do script WordCount deve aparecer na janela de comando:

![Os resultados de frequência da palavra no PowerShell do exemplo de contagem de palavras do MapReduce Hadoop no HDInsight.][image-hdi-sample-wordcount-output]

Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esta amostra novamente, será necessário alterar o nome do arquivo de saída.

##Código-fonte Java

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

## Próximas etapas

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Desenvolver programas Java MapReduce para Hadoop no HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Enviar trabalhos Hadoop no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Amostra: graySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Amostra: estimador de pi][hdinsight-sample-pi-estimator]
* [Amostra: streaming em C#][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->