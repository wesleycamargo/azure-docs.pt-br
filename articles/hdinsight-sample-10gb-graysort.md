<properties 
	pageTitle="O exemplo de GraySort de 10GB | Azure" 
	description="Saiba como executar um GraySort de finalidade geral para quantidades muito grandes de dados, geralmente um mínimo de 100 TB, em Hadoop com HDInsight usando o PowerShell do Azure." 
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
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# Exemplo de Hadoop GraySort de 10 GB no HDInsight
 
Este tópico de exemplo mostra como executar um programa MapReduce de Hadoop GraySort de finalidade geral no Azure HDInsight usando o PowerShell do Azure. Um GraySort é uma espécie de benchmark cuja métrica é a taxa de classificação (TB/minuto) que é obtida durante a classificação de grandes quantidades de dados, geralmente um mínimo de 100 TB. 

Este exemplo usa uma quantidade modesta de 10 GB de dados para que possa ser executado de modo relativamente rápido. Ele usa os aplicativos MapReduce desenvolvidos por Owen O'Malley e Arun Murthy que ganharam o benchmark anual de classificação de terabytes de finalidade geral ("daytona") em 2009, com uma taxa de 0,578 TB/m (100 TB em 173 minutos). Para obter mais informações sobre esse e outros benchmarks de classificação, consulte o site [Sortbenchmark](http://sortbenchmark.org/)   .

Este exemplo usa três conjuntos de programas MapReduce:	
 
1. **O TeraGen** é um programa MapReduce que você pode usar para gerar as linhas de dados a serem classificadas.
2. **O TeraSort** amostra os dados de entrada e usa o MapReduce para classificar os dados em uma ordem total. O TeraSort é uma espécie de padrão de funções MapReduce, exceto por um particionador personalizado que usa uma lista classificada de chaves de exemplo N-1 que definem o intervalo de chaves para cada redução. Em particular, todas as chaves de modo que exemplo[i-1] <= chave < exemplo[i] são enviadas para reduzir i. Isso garante que as saídas da redução de i sejam todas menores do que a saída da redução i+1.
3. **TeraValidate** é um programa MapReduce que valida se a saída é classificada globalmente. Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave é menor ou igual à anterior. A função de mapa também gera registros das primeiras e das últimas chaves de cada arquivo, e a função de redução garante que a primeira chave do arquivo i seja maior do que a última chave do arquivo i-1. Todos os problemas são relatados como saída da redução com as chaves que estão fora de ordem.

O formato de entrada e saída, usado por todos os três aplicativos, lê e grava arquivos de texto no formato correto. A saída da redução tem replicação definida como 1, em vez do padrão 3, porque o concurso de benchmark não exige que os dados de saída sejam replicados em vários nós.

 
**Você aprenderá:**		
* Como usar o PowerShell do Azure para executar uma série de programas MapReduce no Azure HDInsight.		
* A ver qual a aparência de um programa MapReduce escrito em Java.


**Pré-requisitos**	

- Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)](http://azure.microsoft.com/pt-br/pricing/free-trial/).

- Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight](../hdinsight-provision-clusters/)

- Você deve ter instalado o PowerShell do Azure e o configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

## Neste artigo
Este tópico mostra como executar a série de programas MapReduce que compõem o exemplo, apresenta o código Java do programa MapReduce, resume o que você aprendeu e descreve algumas das próximas etapas. Contém as seguintes seções.
	
1. [Executar o exemplo com o PowerShell do Azure](#run-sample)	
2. [O código Java do programa MapReduce TeraSort](#java-code)
3. [Resumo](#summary)	
4. [Próximas etapas](#next-steps)	

<h2><a id="run-sample"></a>Executar o exemplo com o PowerShell do Azure</h2>

Três tarefas são solicitadas pela amostra, cada uma correspondendo a um dos programas MapReduce descritos na introdução:	

1. Gerar os dados para classificação executando o trabalho MapReduce **TeraGen**.	
2. Classificar os dados executando o trabalho MapReduce **TeraSort**.		
3. Confirmar que os dados foram classificados corretamente executando o trabalho MapReduce **TeraValidate**.	


**Para executar o programa TeraGen**	

1. Abra o PowerShell do Azure. Para obter instruções sobre como abrir a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Defina as duas variáveis nos comandos a seguir e execute-as:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"
                 
3. Execute os seguintes comandos para criar uma definição de trabalho MapReduce

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

	> [AZURE.NOTE] *hadoop-examples.jar* é fornecido com clusters HDInsight versão 2.1. O arquivo foi renomeado para *hadoop-mapreduce.jar* em clusters HDInsight versão 3.0.
	
	O argumento *"-Dmapred.map.tasks=50"* especifica que 50 mapas serão criados para executar o trabalho. O argumento *100000000* especifica a quantidade de dados a serem gerados. O argumento final,  */example/data/10GB-sort-input*, especifica o diretório de saída no qual os resultados são salvos (que contém a entrada para o estágio de classificação a seguir).

4. Execute os seguintes comandos para enviar o trabalho, aguarde a conclusão do trabalho e imprima o erro padrão:

		# Run the TeraGen MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName         
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


**Para executar o programa TeraSort**			

1. Abra o PowerShell do Azure.
2. Defina as duas variáveis nos comandos a seguir e execute-as:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"

3. Execute o comando a seguir para definir o trabalho MapReduce: 	 

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

	O argumento *"-Dmapred.map.tasks=50"* especifica que 50 mapas serão criados para executar o trabalho. O argumento *100000000* especifica a quantidade de dados a serem gerados. Os dois últimos argumentos especificam as pastas de entrada e saída. 

4. Execute o seguinte comando para enviar o trabalho, aguarde a conclusão do trabalho e imprima o erro padrão:

		# Run the TeraSort MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job 
		Select-AzureSubscription $subscriptionName        
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


**Para executar o programa TeraValidate**
		 		
1. Abra o PowerShell do Azure.
2. Defina as duas variáveis nos comandos a seguir e execute-as:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"
                 
3. Execute o comando a seguir para definir o trabalho MapReduce: 

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

	O argumento *"-Dmapred.map.tasks=50"* especifica que 50 mapas serão criados para executar o trabalho. O argumento *"-Dmapred.reduce.tasks=25"* especifica que 25 tarefas de redução serão criadas para executar o trabalho. Os dois últimos argumentos especificam as pastas de entrada e saída.  
 

4. Execute os seguintes comandos para enviar o trabalho MapReduce, aguarde a conclusão do trabalho e imprima o erro padrão:

		# Run the TeraSort MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job 
		Select-AzureSubscription $subscriptionName 
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


<h2><a id="java-code"></a>Código Java do programa MapReduce TerraSort</h2>

O código do programa MapReduce TerraSort é apresentado para inspeção nesta seção. 


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one	
	 * or more contributor license agreements.  See the NOTICE file	
	 * distributed with this work for additional information	
	 * regarding copyright ownership.  The ASF licenses this file	
	 * to you under the Apache License, Version 2.0 (the	
	 * "License"); you may not use this file except in compliance	
	 * with the License.  You may obtain a copy of the License at	
	 *	
	 *     http://www.apache.org/licenses/LICENSE-2.0	
	 *	
	 * Unless required by applicable law or agreed to in writing, software	
	 * distributed under the License is distributed on an "AS IS" BASIS,	
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.	
	 * See the License for the specific language governing permissions and	
	 * limitations under the License.	
	 */	
	
	package org.apache.hadoop.examples.terasort;
	
	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;
	
	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;
	
	/**	
	 * Generates the sampled split points, launches the job, 	
	 * and waits for it to finish. 	
	 * <p>
	 * To run the program: 	
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>	
	 */	
	
	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);
	
	  /**	
	   * A partitioner that splits text keys into roughly equal 	
	   * partitions in a global sorted order.	
	   */	
	
	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;
	
	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }
	
	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];
	      
	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }
	
	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }
	
	
	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p, 
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }
	
	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }
	
	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }
	
	    public TotalOrderPartitioner() {
	    }
	
	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }
	    
	  }
	  
	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }
	
	  /**
	   * @param args
	   */
	
	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }
	
	}
  

<h2><a id="summary"></a>Resumo</h2>

Esse exemplo demonstrou como executar uma série de trabalhos MapReduce usando o Azure HDInsight, onde a saída dos dados de um único trabalho se torna a entrada do próximo trabalho da série.

<h2><a id="next-steps"></a>Próximas etapas</h2>

Para obter tutoriais que executam outros exemplos e fornecem instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Exemplo: Vistoriador de Pi][hdinsight-sample-pi-estimator]
* [Exemplo: Contagem de palavras][hdinsight-sample-wordcount]
* [Exemplo: Streaming no C#][hdinsight-sample-csharp-streaming]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o hive com o HDInsight][hdinsight-use-hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx


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
