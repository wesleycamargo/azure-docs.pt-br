<properties 
	pageTitle="Use a ação de Script no HDInsight para instalar o Spark em um cluster do Hadoop | Microsoft Azure"
	description="Saiba como personalizar um cluster do HDInsight com o Spark. Você usará uma opção de configuração de Ação de Script para usar um script para instalar o Spark"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/20/2015"
	ms.author="larryfr"/>

# Instalar e usar o Spark em clusters Hadoop do HDInsight

Neste documento, você aprenderá como instalar o Spark usando a Ação de Script. A Ação de Script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script][hdinsight-cluster-customize]. Depois de ter instalado o Spark, você também aprenderá a executar uma consulta Spark em clusters HDInsight.

> [AZURE.NOTE]O HDInsight também oferece o Spark como um tipo de cluster, o que significa que agora você pode provisionar diretamente um cluster do Spark sem modificar um cluster do Hadoop. No entanto, isso está limitado aos clusters baseados no Windows. Usando o tipo de cluster Spark, você obtém um cluster do HDInsight versão 3.2 do Windows com o Spark versão 1.3.1. Para saber mais, consulte [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).


## <a name="whatis"></a>O que é Spark?

O <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> é uma estrutura de processamento paralelo de código-fonte aberto que dá suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina.

O Spark também podem ser usado para executar o processamento de dados baseados em disco convencional. O Spark melhora a estrutura MapReduce tradicional, evitando gravações em disco nos estágios intermediários. Além disso, o Spark é compatível com o armazenamento de Blob do Azure e com HDFS (Sistema de Arquivos Distribuído Hadoop) para que os dados existentes possam ser processados facilmente por meio do Spark.

Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Spark.

## <a name="whatis"></a>Qual versão do Spark posso instalar?

Neste tópico, usamos um script personalizado de Ação de Script para instalar o Spark em um cluster HDInsight. Esse script instala o Spark 1.3.1.

Você pode modificar esse script ou criar seu próprio script para instalar outras versões do Spark.

## O que o script faz

Esse script instala a versão 1.3.1 do Spark em `/usr/hdp/current/spark`.

## <a name="install"></a>Instalar o Spark usando ações de script

Um exemplo de script para instalar Spark em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente para leitura em [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o portal do Azure.

> [AZURE.NOTE]Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para saber mais sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters de HDInsight baseados em Linux](hdinsight-provision-linux-clusters.md#portal), mas não conclua o provisionamento.

2. Na folha **Configuração opcional**, selecione **Ações de script** e forneça as informações a seguir:

	* __NOME__: insira um nome fácil para a ação de script.
	* __URI do SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh
	* __CABEÇALHO__: marque essa opção
	* __TRABALHO__: marque essa opção
	* __ZOOKEEPER__: marque essa opção para instalar no nó Zookeeper.
	* __PARÂMETROS__: deixe esse campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações da configuração opcional.

4. Continue o provisionamento do cluster, conforme descrito em [Provisionar clusters do HDInsight baseados em Linux](hdinsight-provision-linux-clusters.md#portal).

## <a name="usespark"></a>Como usar o Spark no HDInsight?

O Spark fornece APIs em Scala, Python e Java. Você também pode usar o shell Spark interativo para executar consultas Spark. Quando o cluster tiver terminado o provisionamento, use o seguinte para conectar-se ao cluster HDInsight:

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
Para saber mais sobre como usar SSH com o HDInsight, consulte o seguinte:

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Uma vez conectado, use as seções a seguir para etapas específicas sobre como usar o Spark:

- [Usando o shell do Spark para executar consultas interativas](#sparkshell)
- [Usando o shell do Spark para executar consultas SQL do Spark](#sparksql) 
- [Usando um programa Scala autônomo](#standalone)

###<a name="sparkshell"></a>Usando o shell do Spark para executar consultas interativas

1. Execute o seguinte comando para iniciar o shell Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Depois de concluir a execução do comando, você deve obter um prompt Scala:

		 scala>

5. No prompt Scala, insira a consulta Spark mostrada abaixo. Essa consulta conta a ocorrência de cada palavra no arquivo davinci.txt que está disponível no local /example/data/gutenberg/ no armazenamento de Blob do Azure associado ao cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. O resultado deve ser assim:

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. Digite: q para sair do prompt Scala.

		:q

###<a name="sparksql"></a>Usando o shell do Spark para executar consultas SQL do Spark

O SQL do Spark permite que você use o Spark para executar consultas relacionais expressas em SQL (Structured Query Language), HiveQL ou Scala. Nesta seção, observaremos o uso do Spark para executar uma consulta de Hive em uma tabela de exemplo do Hive. A tabela de Hive usada nesta seção (chamada **hivesampletable**) fica disponível por padrão quando você provisiona um cluster.

1. Execute o seguinte comando para iniciar o shell Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Depois de concluir a execução do comando, você deve obter um prompt Scala:

		 scala>

2. No prompt de Scala, defina o contexto de Hive. Isso é necessário para trabalhar com consultas Hive usando o Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]`sc` nesta instrução é o contexto padrão do Spark, que é definido quando você iniciar o shell do Spark.

5. Execute uma consulta de Hive usando o contexto de Hive e grave a saída no console. A consulta recupera dados em dispositivos de uma fabricação específica e limita o número de registros recuperados a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Você verá algo semelhante ao mostrado a seguir:

		[820,11:35:17,pt-BR,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,pt-BR,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,pt-BR,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. Digite: q para sair do prompt Scala.

		:q

### <a name="standalone"></a>Usando um programa Scala autônomo

Nesta seção, você criará um aplicativo Scala que conta o número de linhas que contêm as letras “a” e “b” em um arquivo de dados de exemplo (/example/data/gutenberg/davinci.txt.)

1. Use os comandos a seguir para instalar a Ferramenta de Compilação Scala:

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	Quando solicitado, selecione __Y__ para continuar.

2. Crie a estrutura do diretório do projeto Scala:

		mkdir -p SimpleScalaApp/src/main/scala
		
3. Crie um novo arquivo chamado __simple.sbt__, que contém as informações de configuração para este projeto.

		cd SimpleScalaApp
		nano simple.sbt
		
	Use o seguinte como conteúdo do arquivo:

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]Lembre-se de manter as linhas em branco entre cada entrada.
	
	Use __Ctrl+X__ e __Y__ e __Enter__ para salvar o arquivo.

4. Use o seguinte comando para criar um novo arquivo chamado __SimpleApp.scala__ no diretório __SimpleScalaApp/src/main/scala__:

		nano src/main/scala/SimpleApp.scala

	Use o seguinte como conteúdo do arquivo:

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

	Use __Ctrl+X__, __Y__ e __Enter__ para salvar o arquivo.

5. No diretório __SimpleScalaApp__, use o seguinte comando para compilar o aplicativo e armazená-lo em um arquivo jar:

		sbt package

	Depois que o aplicativo for compilado, você verá um arquivo **simpleapp\_2.10-1.0.jar** criado no diretório \_\_SimpleScalaApp/target/scala-2.10**.

6. Use o seguinte comando para executar o programa SimpleApp.scala:


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Quando o programa é encerrado, o resultado é exibido no console.

		Lines with a: 21374, Lines with b: 11430

## Próximas etapas

- [Instalar e usar a Matiz em clusters do HDInsight](hdinsight-hadoop-hue-linux.md). A Matiz é uma interface da Web que torna mais fácil criar, executar e salvar trabalhos Pig e Hive, bem como procurar o armazenamento padrão do cluster do HDInsight.

- [Instalar o R em clusters HDInsight][hdinsight-install-r] fornece instruções sobre como usar a personalização do cluster para instalar e usar o R em clusters Hadoop no HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.

- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser usado com o HDInsight do Azure.

- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações poderosas nos dados armazenados.

- [Instalar a Matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização do cluster para instalar a Matiz em clusters de Hadoop do HDInsight. A Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster Hadoop.



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=September15_HO1-->