<properties
	pageTitle="Exemplo de contagem de palavras do MapReduce do Hadoop no HDInsight | Microsoft Azure"
	description="Execute um exemplo de contagem de palavras do MapReduce em um cluster do Hadoop no HDInsight O programa, escrito em Java, contagens de ocorrências de palavras em um arquivo de texto."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015" 
	ms.author="bradsev"/>

#Execute um exemplo de contagem de palavras do MapReduce programado em Java em um cluster do Hadoop no HDInsight

Este tutorial mostra como executar um exemplo de contagem de palavras MapReduce em um cluster do Hadoop no HDInsight. O programa é escrito em Java. Ele conta as ocorrências de palavras em um arquivo de texto e então produz um novo arquivo de texto contendo cada palavra emparelhada com a sua frequência de ocorrência. O arquivo de texto analisado neste exemplo é a edição em eBook do Project Gutenberg de The Notebooks of Leonardo Da Vinci.

> [AZURE.NOTE]As etapas deste documento exigem um cliente do Windows. Para obter etapas sobre como usar o exemplo de contagem de palavras em um cliente Linux, OS X ou Unix, com um cluster do HDInsight para Linux, consulte [Usar o MapReduce com o Hadoop no HDInsight com SSH](hdinsight-hadoop-use-mapreduce-ssh.md) ou [Usar o MapReduce com Hadoop no HDInsight usando Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Você aprenderá a:**

* Como usar o PowerShell do Azure para executar um programa MapReduce em um cluster HDInsight.
* Como escrever programas MapReduce em Java.


**Pré-requisitos**:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Um cluster HDInsight**. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight](hdinsight-provision-clusters.md).

- **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Executar o exemplo usando o PowerShell do Azure</h2>

**Para enviar o trabalho MapReduce**

1.	Abra o console do **PowerShell do Azure**. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina as duas variáveis nos comandos a seguir e execute-as:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Execute os seguintes comandos para criar uma definição de trabalho MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar* é fornecido com clusters do HDInsight versão 2.1. O arquivo foi renomeado *hadoop-mapreduce.jar* em clusters do HDInsight versão 3.0.

	O arquivo hadoop-mapreduce-examples.jar vem com o cluster HDInsight. Existem dois argumentos para o trabalho de MapReduce. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. O arquivo de origem é fornecido com o cluster HDInsight, e o caminho do arquivo de saída será criado no tempo de execução.

6. Execute o comando a seguir para enviar o trabalho MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde deseja executar o trabalho MapReduce.

8. Execute o seguinte comando para verificar erros na execução do trabalho MapReduce:

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**Para recuperar os resultados do trabalho MapReduce**

1. Abra o console do **PowerShell do Azure**.
2. Defina as três variáveis nos comandos a seguir e, em seguida, execute-as:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	A Conta de Armazenamento do Azure é aquela que você criou anteriormente no tutorial. A conta de armazenamento é usada para hospedar o blob usado como o sistema de arquivos do cluster HDInsight padrão. Geralmente, o nome do contêiner de armazenamento de Blob do Azure compartilha o mesmo nome que o cluster HDInsight, a menos que você especifique um nome diferente ao provisionar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** é usado para definir a assinatura atual caso haja várias assinaturas e a assinatura padrão não seja a que você deseja usar.

4. Execute o comando a seguir para baixar a saída do trabalho MapReduce do blob para a estação de trabalho:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta */example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho MapReduce. *part-r-00000* é o nome do arquivo padrão da saída do trabalho MapReduce. O arquivo será baixado na mesma estrutura de pastas na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz de C. O arquivo será baixado na pasta *C:\example\data\WordCountOutput*.

5. Execute o comando a seguir para imprimir a saída do trabalho de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	O trabalho MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens. O script usa o comando **findstr** para listar todas as palavras que contêm *"there"*.

A saída do script WordCount deve aparecer na janela de comando:

![Os resultados de frequência da palavra no PowerShell do exemplo de contagem de palavras do MapReduce Hadoop no HDInsight.][image-hdi-sample-wordcount-output]

Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esta amostra novamente, será necessário alterar o nome do arquivo de saída.

## <a id="java-code"></a>Código Java do programa WordCount do MapReduce</h2>



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



Neste tutorial, você viu como executar um programa MapReduce que conta ocorrências de palavras em um arquivo de texto com o Azure HDInsight usando o PowerShell do Azure.

## <a id="next-steps"></a>Próximas etapas</h2>

Para obter tutoriais que executam outras amostras e fornecem instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Amostra: graySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Amostra: estimador de pi][hdinsight-sample-pi-estimator]
* [Amostra: streaming em C#][hdinsight-sample-cs-streaming]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=62-->