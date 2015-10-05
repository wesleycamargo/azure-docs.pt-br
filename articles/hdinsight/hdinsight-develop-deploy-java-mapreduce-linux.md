<properties
	pageTitle="Desenvolver programas Java MapReduce para Hadoop | Microsoft Azure"
	description="Saiba como desenvolver programas MapReduce em Java no emulador do HDInsight e como implantá-los no HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="larryfr"/>

# Desenvolver programas Java MapReduce para Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

Este documento orienta você quanto ao uso do Apache Maven para criar um aplicativo de MapReduce e implantar e executá-lo em um Hadoop baseado em Linux no cluster do HDInsight.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Uma assinatura do Azure**: consulte [Obter avaliação gratuita do Azure](get-azure-free-trial-for-testing-hadoop-in-hdinsight.md).

- **CLI do Azure**: para obter mais informações, consulte [Instalar e configurar a CLI do Azure](../xplat-cli.md)

##Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA\_HOME** - deve apontar para o diretório onde o Java runtime environment (JRE) está instalado. Por exemplo, em um sistema OS x, Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - deve conter os seguintes caminhos:

	* **JAVA\_HOME** (ou o caminho equivalente)

	* **JAVA\_HOME\\bin** (ou o caminho equivalente)

	* O diretório onde o Maven está instalado

##Criar um novo projeto Maven

1. Em uma sessão do terminal ou linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja armazenar o projeto.

3. Use o comando __mvn__, que é instalado com o Maven, para gerar o scaffolding para o projeto.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Isso criará um novo diretório dentro do diretório atual, com o nome especificado pelo parâmetro __artifactID__ (**wordcountjava** neste exemplo). Esse diretório conterá os itens a seguir:

	* __pom.xml__ - o [POM (Modelo de Objeto de Projeto](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém informações e detalhes de configuração usados para criar o projeto.

	* __src__ - o diretório que contém o diretório __main/java/org/apache/hadoop/examples__, no qual você criará seu aplicativo.

3. Exclua o arquivo __src/test/java/org/apache/hadoop/examples/apptest.java__, já que ele não será utilizado neste exemplo.

##Adicionar dependências

1. Edite o arquivo __pom.xml__ e adicione o seguinte dentro da seção `<dependencies>`:

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	Isso informa ao Maven que o projeto exige as bibliotecas (listadas em <artifactId>) com uma versão específica (listada em <version>). No momento da compilação, ele será baixado do repositório padrão do Maven. Você pode usar a [pesquisa de repositório do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais informações.

	O `<scope>provided</scope>` informa o Maven que essas dependências não devem ser empacotadas com o aplicativo, pois serão fornecidas pelo cluster do HDInsight no tempo de execução.

2. Adicione o conteúdo a seguir ao arquivo __pom.xml__. Isso precisa estar dentro das tags `<project>...</project>` no arquivo; por exemplo, entre `</dependencies>` e `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	O primeiro plug-in configura o [Plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), que é usado para criar um uberjar (às vezes chamado de fatjar), que contém as dependências exigidas pelo aplicativo. Ele também evita a duplicação de licenças no pacote jar, que pode causar problemas em alguns sistemas.

	O segundo plug-in configura o compilador do Maven, que é usado para definir a versão do Java exigida pelo aplicativo como a versão usada no cluster do HDInsight.

3. Salve o arquivo __pom.xml__.

##Criar o aplicativo MapReduce

1. Vá até o diretório __wordcountjava/src/main/java/org/apache/hadoop/examples__ e renomeie o arquivo __App.java__ como __WordCount.java__.

2. Abra o arquivo __WordCount.java__ em seu editor de texto e substitua o conteúdo pelo seguinte:

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

	Observe que o nome do pacote é **org.apache.hadoop.examples** e o nome da classe é **WordCount**. Você usará esses nomes quando enviar o trabalho MapReduce.

3. Salve o arquivo.

##Compilar o aplicativo

1. Altere para o diretório __wordcountjava__ se ainda não estiver lá.

2. Use o comando a seguir para compilar um arquivo JAR contendo o aplicativo:

		mvn clean package

	Isso limpará quaisquer artefatos de compilações anteriores, baixará quaisquer dependências que ainda não tiverem sido instaladas e então compilará e criará o pacote do aplicativo.

3. Assim que o comando for concluído, o diretório __wordcountjava/target__ conterá um arquivo chamado __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]O arquivo __wordcountjava-1.0-SNAPSHOT.jar__ é um uberjar, que contém não apenas o trabalho WordCount, mas também as dependências necessárias ao trabalho em tempo de execução.


##<a id="upload"></a>Carregar o jar

Use o comando a seguir para carregar o arquivo jar para o nó do HDInsight:

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Isso copiará os arquivos do sistema local para o nó principal.

> [AZURE.NOTE]Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Executar o trabalho MapReduce

1. Conecte ao HDInsight usando o SSH conforme descrito nos seguintes artigos:

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Na sessão do SSH, use o seguinte comando para executar o aplicativo MapReduce:

		hadoop jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

	Isso usará o aplicativo WordCount do MapReduce para contar as palavras no arquivo davinci.txt e armazenará os resultados em \___wasb:///example/data/wordcountout__. Os arquivos de entrada e saída são armazenados no armazenamento padrão do cluster.

3. Após concluir o trabalho, use o seguinte para exibir os resultados:

		hadoop fs -cat wasb:///example/data/wordcountout/*

	Você deve receber uma lista de palavras e contagens, com valores semelhantes ao seguinte:

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>Próximas etapas

Neste documento, você aprendeu a desenvolver um trabalho MapReduce em Java. Consulte os seguintes documentos para ver outras maneiras de trabalhar com o HDInsight.

- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Centro de desenvolvedores do Java](/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=Sept15_HO4-->