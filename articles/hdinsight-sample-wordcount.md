<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Amostra de WordCount Hadoop no HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Saiba como executar uma amostra de MapReduce simples no HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Exemplo de WordCount Hadoop no HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Exemplo de WordCount Hadoop no HDInsight

Este tópico de exemplo mostra como executar um programa MapReduce do Hadoop, que conta ocorrências de palavras em um arquivo de texto usando o PowerShell do Azure para executar o programa no Azure HDInsight. O programa WordCount MapReduce é escrito em Java e executado em um cluster do Hadoop gerenciado pelo HDInsight. O arquivo de texto analisado neste exemplo é a edição em eBook do Project Gutenberg de The Notebooks of Leonardo Da Vinci.

O programa MapReduce do Hadoop lê o arquivo de texto e conta quantas vezes cada palavra ocorre. A saída é um novo arquivo de texto que consiste em linhas, cada uma contendo uma palavra e a contagem (um par de chave/valor separados por tabulações) de quantas vezes essa palavra ocorreu no documento. Esse processo é feito em dois estágios. O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Emite um par de chave/valor cada vez que uma palavra ocorre seguido por 1. Em seguida, o redutor soma essas contagens individuais de cada palavra e emite um único par de chave/valor que contém a palavra seguida pela soma de suas ocorrências.

**Você aprenderá a:**

-   Como usar o PowerShell do Azure para executar um programa MapReduce em um cluster HDInsight.
-   Como escrever programas MapReduce em Java.

**Pré-requisitos**:

-   Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)][Testar o Azure gratuitamente (a página pode estar em inglês)].

-   Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight] ou [Provisionar clusters HDInsight][Provisionar clusters HDInsight].

-   Você deve ter instalado o PowerShell do Azure e o configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].

## Neste artigo

Este tópico mostra como executar o exemplo, apresenta o código Java do programa MapReduce, resume o que você aprendeu e descreve algumas das próximas etapas. Contém as seguintes seções.

1.  [Executar o exemplo usando o PowerShell do Azure][Executar o exemplo usando o PowerShell do Azure]
2.  [Código Java do programa WordCount MapReduce][Código Java do programa WordCount MapReduce]
3.  [Resumo][Resumo]
4.  [Próximas etapas][Próximas etapas]

## <span id="run-sample"></span></a>Executar o exemplo usando o PowerShell do Azure

**Para enviar o trabalho MapReduce**

1.  Abra o **PowerShell do Azure**. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].

2.  Defina as duas variáveis nos comandos a seguir e execute-as:

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Execute os seguintes comandos para criar uma definição de trabalho MapReduce:

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] O *hadoop-examples.jar* é fornecido com clusters HDInsight versão 2.1. O arquivo foi renomeado para *hadoop-mapreduce.jar* em clusters HDInsight versão 3.0.

    O arquivo hadoop-examples.jar é fornecido com o cluster HDInsight. Existem dois argumentos para o trabalho de MapReduce. O primeiro é o nome do arquivo de origem e o segundo é o caminho do arquivo de saída. O arquivo de origem é fornecido com o cluster HDInsight, e o caminho do arquivo de saída será criado no tempo de execução.

4.  Execute o comando a seguir para enviar o trabalho MapReduce:

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce.

5.  Execute o seguinte comando para verificar qualquer erro com a execução do trabalho de MapReduce:

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Para recuperar os resultados do trabalho de MapReduce**

1.  Abra o **PowerShell do Azure**.
2.  Defina as três variáveis nos comandos a seguir e, em seguida, execute-as:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

    A Conta de Armazenamento do Azure é aquela que você criou anteriormente no tutorial. A conta de armazenamento é usada para hospedar o contêiner de Blob usado como o sistema de arquivos de cluster HDInsight padrão. Geralmente, o nome do contâiner de armazenamento de Blob compartilham o mesmo nome que o cluster HDInsight a menos que você especifique um nome diferente ao configurar o cluster.

3.  Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure:

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    O *Select-AzureSubscription* é usado para definir a assinatura atual caso você tenha várias assinaturas e a assinatura padrão não é aquela para ser usada.

4.  Execute o comando a seguir para baixar a saída do trabalho de MapReduce do contêiner de Blob para a estação de trabalho:

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    A pasta */example/data/WordCountOutput* é a pasta de saída especificada quando você executa o trabalho MapReduce. *part-r-00000* é o nome do arquivo padrão da saída do trabalho MapReduce. O arquivo será baixado na mesma estrutura de pasta na pasta local. Por exemplo, na seguinte captura de tela, a pasta atual é a pasta raiz de C. O arquivo será baixado na pasta *C:\\example\\data\\WordCountOutput*.

5.  Execute o comando a seguir para imprimir a saída do trabalho de MapReduce:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    O trabalho de MapReduce produz um arquivo chamado *part-r-00000* com as palavras e as contagens. O script usa o comando findstr para listar todas as palavras que contêm *"there"*.

A saída do script WordCount deve aparecer na janela de comando:

![HDI.Sample.WordCount.Output][HDI.Sample.WordCount.Output]

Observe que os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

## <span id="java-code"></span></a>Código Java do programa WordCount MapReduce

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

## <span id="summary"></span></a>Resumo

Neste tutorial, você viu como executar um programa MapReduce que conta ocorrências de palavras em um arquivo de texto com o Azure HDInsight usando o PowerShell do Azure.

## <span id="next-steps"></span></a>Próximas etapas

Para obter tutoriais que executam outros exemplos e fornecem instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]
-   [Amostra: GraySort de 10 GB][Amostra: GraySort de 10 GB]
-   [Amostra: Vistoriador de Pi][Amostra: Vistoriador de Pi]
-   [Amostra: Streaming no C#][Amostra: Streaming no C#]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]
-   [Use o hive com o HDInsight][Use o hive com o HDInsight]
-   [Documentação do SDK do Azure HDInsight][Documentação do SDK do Azure HDInsight]

  [Testar o Azure gratuitamente (a página pode estar em inglês)]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Executar o exemplo usando o PowerShell do Azure]: #run-sample
  [Código Java do programa WordCount MapReduce]: #java-code
  [Resumo]: #summary
  [Próximas etapas]: #next-steps
  [HDI.Sample.WordCount.Output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
  [Amostra: GraySort de 10 GB]: ../hdinsight-sample-10gb-graysort/
  [Amostra: Vistoriador de Pi]: ../hdinsight-sample-pi-estimator/
  [Amostra: Streaming no C#]: ../hdinsight-sample-csharp-streaming/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
  [Documentação do SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
