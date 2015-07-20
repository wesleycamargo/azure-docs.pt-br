<properties
	pageTitle="Exemplo de vistoriador de Pi do Hadoop no HDInsight | Microsoft Azure"
	description="Saiba como executar um estimador de Pi de exemplo do MapReduce do Hadoop no HDInsight."
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
	ms.date="03/30/2015"
	ms.author="bradsev"/>

# A amostra do Hadoop do estimador de pi no HDInsight

Este tópico mostra como executar um programa MapReduce do Hadoop em um HDInsight do Azure que estima o valor da constante matemática pi usando o PowerShell do Azure. Ele também oferece o código Java usado no programa MapReduce, que faz a estimativa do valor de pi para inspeção.

> [AZURE.NOTE]As etapas deste documento exigem um cluster HDInsight baseado em Windows. Para obter informações sobre como executar este e outros exemplos com clusters baseados em Linux, consulte [Executar exemplos do Hadoop no HDInsight](hdinsight-hadoop-run-samples-linux.md)

O programa usa um método estatístico (quasi-Monte Carlo) para estimar o valor de pi. Os pontos colocados aleatoriamente em um quadrado de unidade também caem dentro de um círculo inscrito nesse quadrado com uma probabilidade igual à área do círculo, pi/4. O valor de pi pode ser estimado do valor de 4R onde R é a proporção do número de pontos que estão dentro do círculo em relação ao número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor será a estimativa.

O código Java do estimador de pi que contém as funções de mapeador e de redutor está disponível para inspeção abaixo. O programa mapeador gera um número especificado de pontos colocados aleatoriamente em um quadrado de unidade e, em seguida, conta o número dos pontos que estão dentro do círculo. O programa redutor acumula os pontos contados pelos mapeadores e, em seguida, estima o valor de pi na fórmula 4R, onde R é a proporção do número de pontos contados dentro do círculo em relação ao número total de pontos que estão dentro do quadrado.

O script fornecido para este exemplo envia um trabalho jar do Hadoop e está configurado para executar com um valor de 16 mapas, cada um dos quais é necessário para calcular 10 milhões de pontos de amostra pelos valores do parâmetro. Esses valores de parâmetro podem ser alterados para melhorar o valor estimado de pi. Para referência, as 10 primeiras casas decimais de pi são 3,1415926535.

O arquivo .jar que contém os arquivos necessários para o Hadoop no Azure para implantação do aplicativo é um arquivo .zip e está disponível para download. Você pode descompactá-lo com vários utilitários de compactação e então explorar os arquivos conforme sua conveniência.

Os exemplos que estão disponíveis para ajudar a obter velocidade usando o HDInsight para executar trabalhos MapReduce estão listados em [Executando os exemplos do HDInsight][hdinsight-samples] junto com os links para obter instruções sobre como executá-los.

**Você aprenderá a:**

* Como usar o PowerShell do Azure para executar um programa estimador de pi do MapReduce no Azure HDInsight.
* Ver qual a aparência de um programa MapReduce escrito em Java.

**Pré-requisitos**:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **um cluster do HDInsight**. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [[Provisionar clusters HDInsight](hdinsight-provision-clusters.md).
- **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Executar a amostra com o PowerShell do Azure

**Para enviar o trabalho MapReduce**

1. Abra o PowerShell do Azure. Para obter instruções sobre como usar a janela do console do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Defina as duas variáveis nos comandos a seguir e execute-as:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

4. Execute o comando a seguir para criar uma definição de MapReduce:

		$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "pi" -Arguments "16", "10000000"


	O primeiro argumento indica quantos mapas criar (o padrão é 16). O segundo argumento indica quantas amostras são geradas por mapa (10 milhões por padrão). Portanto, esse programa usa 10*10 milhões = 160 milhões de pontos aleatórios para realizar sua estimativa de pi. O terceiro argumento indica o local e o nome do arquivo jar usado para executar a amostra nos clusters do HDInsight 3.0 e 3.1. (Veja abaixo o conteúdo deste arquivo.)

5. Execute os comandos a seguir para enviar o trabalho MapReduce e aguarde a conclusão do trabalho:

		# Run the pi estimator MapReduce job
		Select-AzureSubscription $subscriptionName
		$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

		# Wait for the job to finish  
		$piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

6. Execute o comando a seguir para recuperar a saída padrão do trabalho MapReduce:

		# Print output and standard error file of the MapReduce job
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

	Para comparação, as 10 primeiras casas decimais de pi são 3,1415926535.


## <a id="java-code"></a>Código Java do programa do MapReduce de vistoriador de Pi



 	/**
 	* Licensed to the Apache Software Foundation (ASF) under one
 	* or more contributor license agreements. See the NOTICE file
 	* distributed with this work for additional information
 	* regarding copyright ownership. The ASF licenses this file
 	* to you under the Apache License, Version 2.0 (the
 	* "License"); you may not use this file except in compliance
 	* with the License. You may obtain a copy of the License at
 	*
	* http://www.apache.org/licenses/LICENSE-2.0
 	*
 	* Unless required by applicable law or agreed to in writing, software
 	* distributed under the License is distributed on an "AS IS" BASIS,
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and
 	* limitations under the License.
 	*/

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi
	//using quasi-Monte Carlo method.
	//
	//Mapper:
	//Generate points in a unit square
	//and then count points inside/outside of the inscribed circle of the square.
	//
	//Reducer:
	//Accumulate points inside/outside results from the mappers.
	//Let numTotal = numInside + numOutside.
	//The fraction numInside/numTotal is a rational approximation of
	//the value (Area of the circle)/(Area of the square),
	//where the area of the inscribed circle is Pi/4
	//and the area of unit square is 1.
	//Then, Pi is estimated value to be 4(numInside/numTotal).
	//

 	public class PiEstimator extends Configured implements Tool {
	//tmp directory for input/output
 	static private final Path TMP_DIR = new Path(
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

	//2-dimensional Halton sequence {H(i)},
	//where H(i) is a 2-dimensional point and i >= 1 is the index.
	//Halton sequence is used to generate sample points for Pi estimation.
 	private static class HaltonSequence {
	// Bases
 	static final int[] P = {2, 3};
	//Maximum number of digits allowed
 	static final int[] K = {63, 40};

 	private long index;
 	private double[] x;
 	private double[][] q;
 	private int[][] d;

	//Initialize to H(startindex),
	//so the sequence begins with H(startindex+1).
 	HaltonSequence(long startindex) {
 	index = startindex;
 	x = new double[K.length];
 	q = new double[K.length][];
 	d = new int[K.length][];
 	for(int i = 0; i < K.length; i++) {
 	q[i] = new double[K[i]];
 	d[i] = new int[K[i]];
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;

 	for(int j = 0; j < K[i]; j++) {
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
 	d[i][j] = (int)(k % P[i]);
 	k = (k - d[i][j])/P[i];
 	x[i] += d[i][j] * q[i][j];
 	}
 	}
 	}

	//Compute next point.
	//Assume the current point is H(index).
	//Compute H(index+1).
	//@return a 2-dimensional point with coordinates in [0,1)^2
 	double[] nextPoint() {
 	index++;
 	for(int i = 0; i < K.length; i++) {
 	for(int j = 0; j < K[i]; j++) {
 	d[i][j]++;
 	x[i] += q[i][j];
 	if (d[i][j] < P[i]) {
 	break;
 	}
 	d[i][j] = 0;
 	x[i] -= (j == 0? 1.0: q[i][j-1]);
 	}
 	}
 	return x;
 	}
 	}

	//Mapper class for Pi estimation.
	//Generate points in a unit square and then
	//count points inside/outside of the inscribed circle of the square.
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.
	//@param offset samples starting from the (offset+1)th sample.
	//@param size the number of samples for this map
	//@param out output {ture->numInside, false->numOutside}
	//@param reporter
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
 	long numInside = 0L;
 	long numOutside = 0L;

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.
	//Accumulate points inside/outside results from the mappers.
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.
 	@Override
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.
	// @param isInside Is the points inside?
	// @param values An iterator to a list of point counts
	// @param output dummy, not used here.
	// @param reporter

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}

 	//Reduce task done, write output to a file.
 	@Override
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class,
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}

	//Run a map/reduce job for estimating Pi.
	//@return the estimated value of Pi.
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	)
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());

 	jobConf.setInputFormat(SequenceFileInputFormat.class);

 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);

 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);

 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);

 	// turn off speculative execution, because DFS doesn't handle
 	// multiple writers to the same file.
 	jobConf.setSpeculativeExecution(false);

 	//setup input/output directories
 	final Path inDir = new Path(TMP_DIR, "in");
 	final Path outDir = new Path(TMP_DIR, "out");
 	FileInputFormat.setInputPaths(jobConf, inDir);
 	FileOutputFormat.setOutputPath(jobConf, outDir);

 	final FileSystem fs = FileSystem.get(jobConf);
 	if (fs.exists(TMP_DIR)) {
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");
	 }
	 if (!fs.mkdirs(inDir)) {
	 throw new IOException("Cannot create input directory " + inDir);
	 }

	 //generate an input file for each map task
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);
	 final LongWritable offset = new LongWritable(i * numPoints);
	 final LongWritable size = new LongWritable(numPoints);
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }

	 //start a map/reduce job
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");

	 //read outputs
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }

	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }

	//Parse arguments and then runs a map/reduce job.
	//Print output in standard out.
	//@return a non-zero if there is an error. Otherwise, return 0.
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);

	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);

	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }

	 //main method for running it as a stand alone command.
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }

## <a id="summary"></a>Resumo

Neste tutorial, você viu como executar um trabalho MapReduce no HDInsight e como usar métodos Monte Carlo que exigem e geram grandes conjuntos de dados que podem ser gerenciados por esse serviço.

Este é o script completo usado para executar esta amostra em um cluster HDInsight 3.1 padrão ou em um cluster 3.0:

	### Provide the Azure subscription name and the HDInsight cluster name
	$subscriptionName = "<SubscriptionName>"
	$clusterName = "<ClusterName>"  

	###Select the Azure subscription to use
	Select-AzureSubscription $subscriptionName

	### Create a MapReduce job definition
	$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -ClassName "pi" –Arguments “32”, “1000000000” -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar"

	### Run the MapReduce job
	$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

	### Wait for the job to finish  
	$piJob | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600

	### Print the standard error file of the MapReduce job
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput



## <a id="next-steps"></a>Próximas etapas

Para obter tutoriais que descrevam outros exemplos e forneçam instruções sobre como usar trabalhos Pig, Hive e MapReduce no Azure HDInsight com o PowerShell do Azure, consulte os seguintes tópicos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Amostra: graySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Amostra: contagem de palavras][hdinsight-sample-wordcount]
* [Amostra: streaming em C#][hdinsight-sample-csharp-streaming]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO2-->