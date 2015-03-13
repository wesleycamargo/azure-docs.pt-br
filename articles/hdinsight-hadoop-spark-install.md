<properties 
	pageTitle="Use a ação de Script no HDInsight para instalar o Spark em cluster| Hadoop Azure" 
	description="Saiba como personalizar o cluster HDInsight para instalar o Spark. Você usará uma opção de configuração de Ação de Script para usar um script para instalar o Spark" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Instalar e usar o Spark 1.0 em clusters Hadoop do HDInsight

Você pode instalar Spark em qualquer tipo de cluster Hadoop do HDInsight usando a personalização de cluster **Ação de Script**. A ação de script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a ação de script][hdinsight-cluster-customize].

Neste tópico, você aprenderá como instalar o Spark 1.0 usando a Ação do Script. Depois de ter instalado o Spark, você também aprenderá a executar uma consulta Spark em clusters HDInsight.


## Neste artigo

- [O que é Spark?](#whatis)
- [Como instalar o Spark?](#install)
- [Como usar o Spark no HDInsight?](#usespark)
- [Instalar o Spark em clusters Hadoop do HDInsight usando o PowerShell](#usingPS)
- [Instalar o Spark em clusters Hadoop do HDInsight usando o SDK do .NET](#usingSDK) 


## <a name="whatis"></a>O que é Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos de grandes volumes de dados analíticos. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina.

O Spark também podem ser usado para executar o processamento de dados baseados em disco convencional. O Spark melhora a estrutura MapReduce tradicional, evitando gravações em disco nos estágios intermediários. Além disso, Spark é compatível com HDFS e WASB para que os dados existentes possam facilmente ser processados usando Spark. 

Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Spark.   

## <a name="install"></a>Como instalar o Spark?

Um exemplo de script para instalar Spark em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente para leitura em [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1). Esta seção fornece instruções sobre como usar o script de exemplo ao provisionar o cluster usando o Portal de Gerenciamento do Azure. 

> [AZURE.NOTE] O script de exemplo funciona apenas com o cluster HDInsight versão 3.1.  Para obter mais informações sobre versões do cluster HDInsight, consulte [versões do cluster HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Inicie o provisionamento de um cluster usando a opção **criação personalizada**, conforme descrito em [Provisionar um cluster usando opções personalizadas](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a Ação de Script, como mostrado abaixo:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar Spark</b>.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI para o script que é chamado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Spark não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
	</table>	

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

Você também pode usar o script para instalar o Spark no HDInsight usando o PowerShell ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste tópico.

## <a name="usespark"></a>Como usar o Spark no HDInsight?
O Spark fornece APIs em Scala, Python e Java. Você também pode usar o shell Spark interativo para executar consultas Spark. Esta seção fornece introdução sobre como usar essas duas abordagens para trabalhar com o Spark:

- [Usando o  shell Spark](#sparkshell)
- [Usando um programa de Scala autônomo](#standalone)

###<a name="sparkshell"></a>Usando o shell Spark
Execute as seguintes etapas para executar consultas Spark de um shell interativo Spark. Nesta seção, executamos uma consulta Spark em um arquivo de dados de exemplo (/example/data/gutenberg/davinci.txt) disponível em clusters HDInsight por padrão.

1. No Portal de Gerenciamento do Azure, habilite a área de trabalho remota para o cluster criado com o Spark instalado e, em seguida, remote no cluster. Para obter instruções, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar aos clusters HDInsight usando o RDP</a>.

2. Na sessão RDP, na área de trabalho, abra a linha de comando do Hadoop e navegue até o local onde o Spark está instalado, por exemplo, **C:\apps\dist\spark-1.0.2**.


3. Execute o seguinte comando para iniciar o shell Spark.

		 .\bin\spark-shell --master yarn

	Depois de concluir a execução do comando, você deve obter um prompt Scala.

		 scala>

5. No prompt Scala, insira a consulta Spark mostrada abaixo. Essa consulta conta a ocorrência de cada palavra no arquivo davinci.txt que está disponível em no exemplo /example/data/gutenberg/ no armazenamento WASB associado ao cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. O resultado deve ser assim:

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. Digite: q para sair do prompt Scala.

		:q

### <a name="standalone"></a>Usando um programa de Scala autônomo

Nesta seção, escrevemos um aplicativo Scala que conta o número de linhas que contêm as letras 'a' and 'b' em um arquivo de dados de exemplo (/example/data/gutenberg/davinci.txt) disponível em clusters HDInsight por padrão. Para escrever e usar um programa de Scala autônomo com um cluster personalizado com instalação de Spark, você deve executar as seguintes etapas:

- Escreva um programa Scala
- Crie-o para obter o arquivo. jar
- Executar o trabalho no cluster

#### Escreva um programa Scala
Nesta seção, você escreve um programa de Scala que conta o número de linhas que contêm 'a' and 'b' no arquivo de dados de exemplo. 

1. Abra um editor de texto e cole o seguinte código:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
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
Nesta seção, você deve usar <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Ferramenta de construção simples</a> (ou sbt) para compilar o programa scala. sbt requer Java 1.6 ou posterior, portanto, verifique se você tem a versão correta do Java instalado antes de continuar com esta seção.

1. Instale sbt de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Crie uma pasta chamada **SimpleScalaApp** e dentro dessa pasta, crie um arquivo chamado **simple.sbt**. Este é um arquivo de configuração que contém informações sobre a versão Scala, dependências da biblioteca, etc. Cole o seguinte no arquivo simple.sbt e salve-o.


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] Lembre-se de manter as linhas em branco no arquivo.

	
3. Sob a pasta **SimpleScalaApp**, crie uma estrutura de diretório **\src\main\scala** e cole o programa Scala (**SimpleApp.scala**) criado anteriormente na pasta \src\main\scala.
4. Abra um prompt de comando, navegue até o diretório SimpleScalaApp e digite o seguinte comando:


		sbt package


	Depois que o aplicativo for compilado, você verá um **simpleapp_2.10 1.0.jar** criado no diretório **\target\scala-2.10** na pasta raiz SimpleScalaApp.


#### Executar o trabalho no cluster
Nesta seção você irá remoto para o cluster com velas instalada e, em seguida, copie a pasta de destino do projeto SimpleScalaApp. Depois, você usará o comando **spark-submit** para enviar o trabalho no cluster.

1. Remoto para o cluster com Spark instalado. No computador em que você escreveu e criou o programa SimpleApp.scala, copie a pasta **SimpleScalaApp\target** e cole-o em um local no cluster.
2. Na sessão RDP, na área de trabalho, abra a linha de comando do Hadoop e navegue até o local que você colou a pasta **destino**.
3. Digite o seguinte comando para executar o programa SimpleApp.scala:


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Quando o programa é encerrado, o resultado é exibido no console.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Instalar o Spark em clusters Hadoop do HDInsight usando o PowerShell

Nesta seção, usamos o cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para chamar scripts usando a Ação de Script para personalizar um cluster. Antes de prosseguir, certifique-se de ter instalado e configurado o PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Execute as seguintes etapas:

1. Abra uma janela do PowerShell do Azure e declare as variáveis a seguir:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Especifique os valores de configuração como nós no cluster e o armazenamento padrão a ser usado.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use o cmdlet**Add-AzureHDInsightScriptAction** para adicionar a ação de Script na configuração do cluster. Posteriormente, quando o cluster estiver sendo criado, a Ação de Script será executada. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

	O cmdlet **Add-AzureHDInsightScriptAction** usa os seguintes parâmetros:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parâmetro</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definição</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">O objeto de configuração em que a informações da ação de script é adicionada</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome da ação de script</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica os nós em que o script de personalização é executado. Os valores válidos são HeadNode (para instalar o headnode) ou DataNode (para instalar em todos os datanodes). Você pode usar um ou ambos os valores.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica o URI para o script que é executado</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parâmetros exigidos pelo script. O script de exemplo usado neste tópico não requer nenhum parâmetro e, portanto, você não vir esse parâmetro no trecho acima.
	</td></tr>
	</table>
	
4. Finalmente, inicie o provisionamento de um cluster personalizados com o Spark instalado.  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando solicitado, insira as credenciais para o cluster. Pode levar alguns minutos até que o cluster seja criado.


## <a name="usingSDK"></a>Instalar o Spark em clusters de HDInsight Hadoop usando o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET. Esta seção fornece instruções sobre como usar a ação de Script do SDK para provisionar um cluster com o Spark instalado. Os procedimentos a seguir devem ser executados:

- Instale o SDK do HDInsight .NET
- Criar um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Para criar um aplicativo do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu Arquivo, clique em **Novo** e **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Aplicativo</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona as bibliotecas .NET e referências a elas a partir do projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes com o botão direito do mouse em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Na função Main (), copie e cole o seguinte código e forneça valores para as variáveis:
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Acrescente o seguinte código à função Main () para usar a classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para chamar um script personalizado para instalar o Spark.

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. E por último, crie o cluster.

		client.CreateCluster(clusterInfo);

11. Salve as alterações no aplicativo e crie a solução. 

**Para executar o aplicativo**

Abra um console do PowerShell, navegue até o local onde você salvou o projeto no Visual Studio, navegue até a pasta \bin\debug do projeto e, em seguida, execute o seguinte comando:

	.\CreateSparkCluster <cluster-name>

Forneça um nome de cluster e pressione ENTER para provisionar um cluster com o Spark instalado.


## Consulte também##
- [Instalar R em clusters HDInsight][hdinsight-install-r] fornece instruções sobre como usar a personalização do cluster para instalar e usar o R em clusters de Hadoop do HDinsight. R é um ambiente de computação de estatísticas e linguagem de fonte aberta e fornece centenas de funções estatísticas de compilação e sua própria linguagem de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos.
- [Instalar o Giraph em clusters HDInsight](../hdinsight-hadoop-giraph-install). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.
- [Instalar o Solr em clusters HDInsight](../hdinsight-hadoop-solr-install). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/
\<!--HONumber=42-->
