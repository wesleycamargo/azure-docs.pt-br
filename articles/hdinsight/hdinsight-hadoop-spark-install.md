<properties
	pageTitle="Use a ação de Script no HDInsight para instalar o Spark em um cluster do Hadoop | Microsoft Azure"
	description="Saiba como personalizar um cluster HDInsight com o Spark usando a Ação de Script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Instalar e usar o Spark em clusters Hadoop do HDInsight


Saiba como instalar o Spark no HDInsight baseado no Windows usando a Ação de Script e como executar consultas Spark em clusters HDInsight.

O HDInsight oferece o Spark como um tipo de cluster de primeira classe para clusters baseados no Windows, o que significa que agora você pode criar diretamente um cluster do Spark sem modificar um cluster do Hadoop. Usando o tipo de cluster Spark, você obtém um cluster do HDInsight versão 3.2 com o Spark versão 1.3.1. Para instalar versões diferentes do Spark, você poderá usar uma Ação de Script. O HDInsight fornece um exemplo de script de Ação de Script.

**Artigos relacionados** - [Instalar o Spark em clusters HDInsight baseados em Linux](hdinsight-hadoop-spark-install-linux.md). - [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters HDInsight. - [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md): criar um cluster do tipo Spark no sistema operacional Windows. - [Personalizar o cluster HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: informações gerais sobre a personalização de clusters HDInsight usando a Ação de Script. - [Desenvolver scripts da Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).

## O que é Spark?

O <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> é uma estrutura de processamento paralelo de código-fonte aberto que dá suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina.

O Spark também podem ser usado para executar o processamento de dados baseados em disco convencional. O Spark melhora a estrutura MapReduce tradicional, evitando gravações em disco nos estágios intermediários. Além disso, o Spark é compatível com o armazenamento de Blob do Azure e com HDFS (Sistema de Arquivos Distribuído Hadoop) para que os dados existentes possam ser processados facilmente por meio do Spark.

Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Spark.

## Instalar o Spark usando o portal de Visualização do Azure

Um exemplo de script para instalar o Spark em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Esse script pode instalar o Spark 1.2.0 ou Spark 1.0.2, dependendo da versão do cluster HDInsight que você criar.

- Se você usar o script durante a criação de um cluster **HDInsight 3.2**, ele instala o **Spark 1.2.0**.
- Se você usar o script durante a criação de um cluster **HDInsight 3.1**, ele instalará o **Spark 1.0.2**.

Você pode modificar esse script ou criar seu próprio script para instalar outras versões do Spark.

> [AZURE.NOTE]O script de exemplo funciona apenas com os clusters HDInsight versões 3.1 e 3.2. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).

1. Inicie a criação de um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, conforme descrito em [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md#portal). Selecione a versão do cluster dependendo do seguinte:

	- Se você deseja instalar o **Spark 1.2.0**, crie um cluster HDInsight 3.2.
	- Se você deseja instalar o **Spark 1.0.2**, crie um cluster HDInsight 3.1.


2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Usar Ação de Script para personalizar um cluster")

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar Spark</b>.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Spark não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para começar a criar o cluster.

Você também pode usar o script para instalar o Spark no HDInsight usando o PowerShell do Azure ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## Usar o Spark no HDInsight
O Spark fornece APIs em Scala, Python e Java. Você também pode usar o shell Spark interativo para executar consultas Spark. Esta seção fornece instruções sobre como usar as diferentes abordagens para trabalhar com o Spark:

- [Usar o shell do Spark para executar consultas interativas](#sparkshell)
- [Usar o shell do Spark para executar consultas SQL do Spark](#sparksql)
- [Usar um programa Scala autônomo](#standalone)

###<a name="sparkshell"></a>Usar o shell do Spark para executar consultas interativas
Execute as seguintes etapas para executar consultas Spark de um shell interativo Spark. Nesta seção, executamos uma consulta Spark em um arquivo de dados de exemplo (/example/data/gutenberg/davinci.txt) disponível em clusters HDInsight por padrão.

1. No Portal do Azure, habilite a Área de Trabalho Remota para o cluster criado com o Spark instalado e, em seguida, acesse remotamente o cluster. Para instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.

2. Na sessão RDP (protocolo RDP), na área de trabalho, abra a linha de comando (em um atalho na área de trabalho) do Hadoop e navegue até o local onde o Spark está instalado, por exemplo, **C:\\apps\\dist\\spark-1.2.0**.


3. Execute o seguinte comando para iniciar o shell Spark:

		 .\bin\spark-shell --master yarn

	Depois de concluir a execução do comando, você deve obter um prompt Scala:

		 scala>

5. No prompt Scala, insira a consulta Spark mostrada abaixo. Essa consulta conta a ocorrência de cada palavra no arquivo davinci.txt que está disponível no local /example/data/gutenberg/ no armazenamento de Blob do Azure associado ao cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. O resultado deve ser assim:

	![Resultado da execução do shell interativo do Scala em um cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Digite: q para sair do prompt Scala.

		:q

###<a name="sparksql"></a>Usar o shell do Spark para executar consultas SQL do Spark

O SQL do Spark permite que você use o Spark para executar consultas relacionais expressas em SQL (Structured Query Language), HiveQL ou Scala. Nesta seção, observaremos o uso do Spark para executar uma consulta de Hive em uma tabela de exemplo do Hive. A tabela do Hive usada nesta seção (chamada **hivesampletable**) fica disponível por padrão quando você cria um cluster.

>[AZURE.NOTE]O exemplo abaixo foi criado no **Spark 1.2.0**, que será instalado se você executar a ação de script durante a criação do cluster HDInsight 3.2.

1. No Portal do Azure, habilite a Área de Trabalho Remota para o cluster criado com o Spark instalado e, em seguida, acesse remotamente o cluster. Para instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.

2. Na sessão RDP, na área de trabalho, abra a linha de comando (em um atalho na área de trabalho) do Hadoop e navegue até o local onde o Spark está instalado, por exemplo, **C:\\apps\\dist\\spark-1.2.0**.


3. Execute o seguinte comando para iniciar o shell Spark:

		 .\bin\spark-shell --master yarn

	Depois de concluir a execução do comando, você deve obter um prompt Scala:

		 scala>

4. No prompt de Scala, defina o contexto de Hive. Isso é necessário para trabalhar com consultas Hive usando o Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Observe que **sc** é o contexto padrão do Spark, que é definido quando você iniciar o shell do Spark.

5. Execute uma consulta de Hive usando o contexto de Hive e grave a saída no console. A consulta recupera dados em dispositivos de uma fabricação específica e limita o número de registros recuperados a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Você verá algo semelhante ao mostrado a seguir:

	![Resultado da execução do SQL do Spark em um cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Digite: q para sair do prompt Scala.

		:q

### <a name="standalone"></a>Usar um programa Scala autônomo

Nesta seção, escrevemos um aplicativo Scala que conta o número de linhas que contêm as letras “a” e “b” em um arquivo de dados de exemplo (/example/data/gutenberg/davinci.txt) disponível em clusters HDInsight por padrão. Para escrever e usar um programa de Scala autônomo com um cluster personalizado com instalação de Spark, você deve executar as seguintes etapas:

- Escreva um programa Scala
- Compilar o programa Scala para obter o arquivo .jar
- Executar o trabalho no cluster

#### Escreva um programa Scala
Nesta seção, você escreve um programa Scala, que conta o número de linhas que contêm 'a' e 'b' no arquivo de dados de exemplo.

1. Abra um editor de texto e cole o seguinte código:


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

2. Salve o arquivo com o nome **SimpleApp.scala**.

#### Compile o programa Scala
Nesta seção, você usa a <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Ferramenta Simples de Compilação</a> (ou SBT) para compilar o programa Scala. A SBT requer Java 1.6 ou posterior, portanto, verifique se você tem a versão correta do Java instalado antes de continuar com esta seção.

1. Instalar SBT por meio de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Crie uma pasta chamada **SimpleScalaApp** e, dentro dessa pasta, crie um arquivo chamado **simple.sbt**. Este é um arquivo de configuração que contém informações sobre a versão Scala, dependências da biblioteca, etc. Cole o conteúdo a seguir no arquivo simple.sbt e salve-o.


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Lembre-se de manter as linhas em branco no arquivo.


3. Na pasta **SimpleScalaApp**, crie uma estrutura de diretório **\\src\\main\\scala** e cole o programa Scala (**SimpleApp.scala**) que você criou anteriormente na pasta \\src\\main\\scala.
4. Abra um prompt de comando, navegue até o diretório SimpleScalaApp e digite o seguinte comando:


		sbt package


	Depois que o aplicativo for compilado, você verá um arquivo **simpleapp\_2.10-1.0.jar** criado no diretório **\\target\\scala-2.10** contida na pasta raiz SimpleScalaApp.


#### Executar o trabalho no cluster
Nesta seção, você conecta-se remotamente ao cluster com o Spark instalado e, em seguida, copia a pasta de destino do projeto SimpleScalaApp. Depois, você usará o comando **spark-submit** para enviar o trabalho no cluster.

1. Remoto para o cluster com Spark instalado. No computador em que você escreveu e criou o programa SimpleApp.scala, copie a pasta **SimpleScalaApp\\target** e cole-a em um local no cluster.
2. Na sessão RDP, na área de trabalho, abra a linha de comando do Hadoop e navegue até o local que você colou a pasta **target**.
3. Digite o seguinte comando para executar o programa SimpleApp.scala:


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Quando o programa é encerrado, o resultado é exibido no console.


		Lines with a: 21374, Lines with b: 11430

## Instalar o Spark usando o Azure PowerShell

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, verifique se você instalou e configurou o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do Azure para HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declare as variáveis a seguir:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Use o cmdlet **Add-AzureHDInsightScriptAction** para adicionar uma Ação de Script à configuração do cluster. Mais tarde, quando o cluster estiver sendo criado, a ação de script será executada.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os parâmetros a seguir:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração em que a informações da ação de script é adicionada.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós em que o script de personalização é executado. Os valores válidos são HeadNode (para instalar no nó principal) ou DataNode (para instalar em todos os nós de dados). Você pode usar um ou ambos os valores.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
</td></tr>
</table>

4. Por fim, inicie a criação de um cluster personalizado com o Spark instalado.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.

## Instalar o Spark usando o PowerShell

Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## Instalar o Spark usando o SDK do .NET

Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## Consulte também

- [Instalar o Spark em clusters HDInsight baseados em Linux](hdinsight-hadoop-spark-install-linux.md): instalar o Spark em clusters HDInsight baseados em Linux.
- [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): criar clusters HDInsight.
- [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md): introdução ao Spark no HDInsight.
- [Personalizar o cluster HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: personalizar o cluster HDInsight usando a Ação de Script.
- [Desenvolver scripts da Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md): desenvolver scripts da Ação de Script.
- [Instalar o R em clusters HDInsight][hdinsight-install-r] fornece instruções sobre como usar a personalização do cluster para instalar e usar o R em clusters Hadoop no HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser usado com o HDInsight do Azure.
- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite executar operações de pesquisa avançadas nos dados armazenados.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md

<!---HONumber=Oct15_HO3-->