<properties linkid="manage-services-hdinsight-sample-wordcount" urlDisplayName="Exemplos do HDInsight" pageTitle="Exemplo de WordCount do HDInsight | Azure" metaKeywords="hdinsight, exemplo do hdinsight, mapreduce" description="Saiba como executar um exemplo simples do MapReduce no HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Exemplo de WordCount do HDInsight" authors="bradsev" />

#Exemplo de WordCount do HDInsight
 
Este tópico de exemplo mostra como executar um programa MapReduce que conta ocorrências de palavras em um arquivo de texto com o Azure HDInsight usando o PowerShell do Azure. O programa WordCount MapReduce é escrito em Java e é executado em um cluster HDInsight. O arquivo de texto analisado aqui é a edição do eBook do Project Gutenberg de The Notebooks of Leonardo Da Vinci. 

O programa MapReduce do Hadoop lê o arquivo de texto e conta quantas vezes cada palavra ocorre. A saída é um novo arquivo de texto que consiste em linhas, cada uma contendo uma palavra e a contagem (um par de chave/valor separados por tabulações) de quantas vezes essa palavra ocorreu no documento. Esse processo é feito em dois estágios. O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Emite um par de chave/valor cada vez que uma palavra ocorre seguido por 1. Em seguida, o redutor soma essas contagens individuais de cada palavra e emite um único par de chave/valor que contém a palavra seguida pela soma de suas ocorrências.

 
**Você irá aprender:**
		
* Como usar o PowerShell do Azure para executar um programa MapReduce em um cluster HDInsight.
* Como escrever programas MapReduce em Java.


**Pré-requisitos**:	

- Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/pricing/free-trial/).

- Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight](/pt-br/manage/services/hdinsight/provision-hdinsight-clusters/).

- Você deve ter instalado o PowerShell do Azure e tê-lo configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

##Neste artigo	
Este tópico mostra como executar o exemplo, apresenta o código Java do programa MapReduce, resume o que você aprendeu e descreve algumas das próximas etapas. Contém as seguintes seções.
	
1. [Executar o exemplo usando o PowerShell do Azure](#run-sample)	
2. [Código Java do programa WordCount MapReduce](#java-code)
3. [Resumo](#summary)	
4. [Próximas etapas](#next-steps)	

<h2><a id="run-sample"></a>Executar o exemplo usando o PowerShell do Azure</h2> 

**Para enviar o trabalho MapReduce**

1.	Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

3. Defina as duas variáveis nos comandos a seguir e execute-as:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
5. Execute o comando a seguir para criar uma definição de trabalho MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [WACOM.NOTE] *hadoop-examples.jar* é fornecido com os clusters HDInsight versão 2.1. O arquivo foi renomeado para *hadoop-mapreduce.jar* em clusters HDInsight versão 3.0.
	
	O arquivo hadoop-examples.jar é fornecido com o cluster HDInsight. Existem dois argumentos para o trabalho de MapReduce. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. O arquivo de origem é fornecido com o cluster HDInsight, e o caminho do arquivo de saída será criado no tempo de execução.

6. Execute o comando a seguir para enviar o trabalho MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce.

8. Execute o seguinte comando para verificar qualquer erro na execução do trabalho MapReduce:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**Para recuperar os resultados do trabalho de MapReduce**

1. Abra o **PowerShell do Azure**.
2. Defina as três variáveis nos comandos a seguir e, em seguida, execute-as:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	A Conta de Armazenamento do Azure é aquela que você criou anteriormente no tutorial. A conta de armazenamento é usada para hospedar o contêiner de Blob usado como o sistema de arquivos de cluster HDInsight padrão.  Geralmente, o nome do contâiner de armazenamento de Blob compartilham o mesmo nome que o cluster HDInsight a menos que você especifique um nome diferente ao configurar o cluster.

3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	O *Select-AzureSubscription* é usado para definir a assinatura atual caso você tenha várias assinaturas e a assinatura padrão não é aquela para ser usada. 

4. Execute o comando a seguir para baixar a saída do trabalho MapReduce do contêiner de Blob na estação de trabalho:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	A pasta */example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho MapReduce. *part-r-00000* é o nome do arquivo padrão da saída do trabalho MapReduce.  O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz de C.  O arquivo será baixado na pasta *C:\example\data\WordCountOutput*. 

5. Execute o comando a seguir para imprimir a saída do trabalho MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	O trabalho de MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens.  O script usa o comando findstr para listar todas as palavras que contém *"there"*.

A saída do script WordCount deve aparecer na janela de comando:

![HDI.Sample.WordCount.Output][image-hdi-sample-wordcount-output]

Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

<h2><a id="java-code"></a>Código Java do programa WordCount MapReduce</h2>



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



<h2><a id="summary"></a>Resumo</h2>

Neste tutorial, você viu como executar um programa MapReduce que conta ocorrências de palavras em um arquivo de texto com o Azure HDInsight usando o PowerShell do Azure.

<h2><a id="next-steps"></a>Próximas etapas</h2>

Para obter tutoriais que executam outros exemplos e fornecem instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Exemplo: GraySort de 10 GB][10gb-graysort]
* [Exemplo: Estimador de Pi][pi-estimator]
* [Exemplo: Streaming no C#][cs-streaming]
* [Use o Pig com o HDInsight][pig]
* [Use o hive com o HDInsight][hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

[10gb-graysort]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/
[pi-estimator]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/
[cs-streaming]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/


[hive]: /pt-br/manage/services/hdinsight/using-hive-with-hdinsight/
[pig]: /pt-br/manage/services/hdinsight/using-pig-with-hdinsight/
 
[hdinsight-configure-powershell]: /pt-br/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/
[hdinsight-get-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/

[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png



