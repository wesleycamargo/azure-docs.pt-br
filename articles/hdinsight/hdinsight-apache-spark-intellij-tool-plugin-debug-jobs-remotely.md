 <properties
	pageTitle="Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar remotamente os aplicativos executados em clusters HDInsight Spark | Microsoft Azure"
	description="Saiba como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar remotamente os aplicativos executados em clusters HDInsight Spark."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="nitinme"/>


# Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente para um cluster do HDInsight Spark Linux

Este artigo oferece diretrizes passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para enviar um trabalho do Spark no cluster HDInsight Spark e depurá-lo remotamente do seu computador desktop. Para fazer isso, você deve executar as seguintes etapas de alto nível:

1. Crie uma Rede Virtual do Azure site a site ou ponto a site. As etapas neste documento pressupõem que você esteja usando uma rede site a site.

2. Crie um cluster Spark no Azure HDInsight que faça parte da Rede Virtual do Azure site a site.

3. Verifique a conectividade entre o nó de cabeçalho do cluster e seu desktop.

4. Crie um aplicativo Scala no IntelliJ IDEA e configure-o para a depuração remota.

5. Execute e depure o aplicativo.

##Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
 
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
 
* IntelliJ IDEA. Este artigo usa a versão 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).
 
* Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ. As Ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas do Azure para IntelliJ. Para obter instruções sobre como instalar o Kit de Ferramentas do Azure, veja [Instalando o Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

* Faça logon em sua assinatura do Azure no IntelliJ IDEA. Siga as instruções [aqui](hdinsight-apache-spark-intellij-tool-plugin.md#log-into-your-azure-subscription).
 
* Durante a execução do aplicativo Spark Scala para depuração remota em um computador com Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que ocorre devido a à ausência do WinUtils.exe no Windows. Para solucionar esse erro, você deve [baixar aqui o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\\WinUtils\\bin**. Em seguida, adicione uma variável de ambiente **HADOOP\_HOME** e defina o valor da variável como **C\\WinUtils**.

## Etapa 1: Criar uma rede virtual do Azure

Siga as instruções dos links a seguir para criar uma Rede Virtual do Azure e então verifique a conectividade entre o desktop e a Rede Virtual do Azure.

* [Criar uma Rede Virtual com uma conexão VPN site a site usando o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar uma Rede Virtual com uma conexão VPN site a site usando o PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## Etapa 2: Criar um cluster HDInsight Spark

Você também deve criar um cluster Apache Spark no Azure HDInsight que faça parte da Rede Virtual do Azure criada. Use as informações disponíveis em [Criar clusters baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a Rede Virtual do Azure que você criou na etapa anterior.

## Etapa 3: Verificar a conectividade entre o nó de cabeçalho do cluster e seu desktop

1. Obter endereço IP do nó de cabeçalho. Abra a IU do Ambari para o cluster. Na folha do cluster, clique em **Painel**.

	![Localizar o IP do nó de cabeçalho](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)

2. Na IU do Ambari, no canto superior direito, clique em **Hosts**.

	![Localizar o IP do nó de cabeçalho](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)

3. Você deve ver uma lista de nós de cabeçalho, os nós de trabalho e os nós do zookeeper. Os nós de cabeçalho têm o prefixo **hn***. Clique no primeiro nó de cabeçalho.

	![Localizar o IP do nó de cabeçalho](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)

4. Na parte inferior da página , na caixa **Resumo**, copie o endereço IP do nó de cabeçalho e o nome do host.

	![Localizar o IP do nó de cabeçalho](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)

5. Inclua o endereço IP e o nome do host do nó de cabeçalho no arquivo **hosts** no computador de onde você deseja executar e depurar os trabalhos Spark remotamente. Isso permitirá que você se comunique com o nó de cabeçalho usando o endereço IP, bem como o nome do host.

	1. Abra um bloco de notas com permissões elevadas. No menu Arquivo, clique em **Abrir** e navegue até o local do arquivo hosts. Em um computador com o Windows, é `C:\Windows\System32\Drivers\etc\hosts`.

	2. Adicione o seguinte ao arquivo **hosts**.

			# For headnode0
			192.xxx.xx.xx hn0-nitinp
			192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

			# For headnode1
			192.xxx.xx.xx hn1-nitinp
			192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net


5. No computador conectado à Rede Virtual do Azure usada pelo cluster HDInsight, verifique se você pode executar ping nos dois nós de cabeçalho usando o endereço IP, bem como o nome do host.

6. Use o SSH para acessar o nó de cabeçalho do cluster usando as instruções em [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster). Do nó de cabeçalho do cluster, execute ping no endereço IP do computador desktop. Você deve testar a conectividade para os endereços IP atribuídos ao computador, um para a conexão de rede e outro para a Rede Virtual do Azure à qual o computador está conectado.

7. Repita estas etapas no outro nó de cabeçalho.

## Etapa 4: Criar um aplicativo Scala Spark usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ e configurá-lo para a depuração remota

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, selecione **Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Na próxima janela, forneça os detalhes do projeto.

	* Forneça um nome de projeto e o local do projeto.
	* Para **SDK do Projeto**, forneça uma versão de Java superior à 7.
	* Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada. **Não use a versão 2.11.x**. Este exemplo usa a versão **2.10.6**.

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-version.png)

	* Para o **SDK do Spark**, baixe e use o SDK [deste local](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Você também pode ignorar isso e usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), no entanto, certifique-se de ter o repositório maven correto instalado para desenvolver aplicativos Spark. (Por exemplo, você precisará certificar-se de que a parte do Streaming Spark está instalada se estiver usando o Streaming Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 - não use o repositório marcado como Scala 2.11.)

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)

	* Clique em **Concluir**.

3. O projeto do Spark criará automaticamente um artefato para você. Para ver o artefato, siga estas etapas.

	1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
	2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para ver o artefato padrão criado.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)

	Você também pode criar seu próprio artefato clicando no ícone **+**, realçado na imagem acima.

4. Na caixa de diálogo **Estrutura do Projeto**, clique em **Projeto**. Se o **SDK do Projeto** for definido como 1.8, defina **Nível de linguagem do projeto** como **7 - Diamonds, ARM, multi-catch etc**.

	![Definir o nível de linguagem do projeto](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/set-project-language-level.png)

4. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, clique com o botão direito do mouse no nome do projeto na árvore do projeto e clique em **Abrir Configurações do Módulo**. Na caixa de diálogo **Estrutura do Projeto**, no painel esquerdo, clique em **Bibliotecas**, clique no símbolo (+) e clique em **Do Maven**.

	![Adicionar biblioteca](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

	Na caixa de diálogo **Baixar a Biblioteca do Repositório Maven**, pesquise e adicione as bibliotecas a seguir.

	* `org.scalatest:scalatest_2.10:2.2.1`
	* `org.apache.hadoop:hadoop-azure:2.7.1`

5. Copie `yarn-site.xml` e `core-site.xml` do nó de cabeçalho do cluster e os adicione ao projeto. Use os comandos a seguir para copiar os arquivos. Você pode usar [Cygwin](https://cygwin.com/install.html) para executar os seguintes comandos `scp` para copiar os arquivos dos nós de cabeçalho do cluster.

		scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

	Como nós já adicionamos o endereço IP e os nomes do host do nó de cabeçalho do cluster e para o arquivos hosts no desktop, podemos usar os comandos **scp** da seguinte maneira.

		scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
		scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

	Adicione esses arquivos ao seu projeto copiando-da pasta **/src** na sua árvore de projeto, por exemplo, `<your project directory>\src`.

6. Atualize o `core-site.xml` para fazer as alterações a seguir.

	1. O `core-site.xml` inclui a chave criptografada para a conta de armazenamento associada ao cluster. No `core-site.xml` que você adicionou ao projeto, substitua a chave criptografada pela chave de armazenamento real associada à conta de armazenamento padrão. Confira [Gerenciar as chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#manage-your-storage-account).

			<property>
	      		<name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
	      		<value>access-key-associated-with-the-account</value>
	    	</property>

	2. Remova as entradas a seguir do `core-site.xml`.

			<property>
      			<name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
      			<value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
			</property>

			<property>
		      	<name>fs.azure.shellkeyprovider.script</name>
		      	<value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
		    </property>

			<property>
      			<name>net.topology.script.file.name</name>
      			<value>/etc/hadoop/conf/topology_script.py</value>
    		</property>

	3. Salve o arquivo.

7. Adicione a classe Main ao seu aplicativo. No **Gerenciador de Projetos**, clique com o botão direito do mouse em **src**, aponte para **Novo** e clique em **Classe do Scala**.

	![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

8. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, para **Tipo** escolha **Objeto** e clique em **OK**.

	![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

9. No arquivo `MyClusterAppMain.scala`, cole o código a seguir. Esse código cria o contexto do Spark e inicia um método `executeJob` do objeto `SparkSample`.


		import org.apache.spark.{SparkConf, SparkContext}

		object SparkSampleMain {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		                           "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		                           "wasbs:///HVACOut")
		  }
		}

10. Repita as etapas 8 e 9 acima para adicionar um novo objeto Scala chamado `SparkSample`. Para essa classe, adicione o código a seguir. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sétima coluna no CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

		import org.apache.spark.SparkContext
	
		object SparkSample {
		  def executeJob (sc: SparkContext, input: String, output: String): Unit = {
		    val rdd = sc.textFile(input)
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
		    println(s)
		
		    rdd1.saveAsTextFile(output)
		    //rdd1.collect().foreach(println)
		  }
		
		}

11. Repita as etapas 8 e 9 acima para adicionar uma nova classe chamada `RemoteClusterDebugging`. Essa classe implementa a estrutura de teste Spark usada para depuração de aplicativos. Adicione o código a seguir à classe `RemoteClusterDebugging`.

		import org.apache.spark.{SparkConf, SparkContext}
		import org.scalatest.FunSuite
		
		class RemoteClusterDebugging extends FunSuite {
		
		  test("Remote run") {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .setMaster("yarn-client")
		                              .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
		                              .set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
		                              .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		      "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		      "wasbs:///HVACOut")
		  }
		}

	Há dois aspectos importantes a serem observados:
	
	* Para `.set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, verifique se o assembly JAR do Spark está disponível no armazenamento de cluster no caminho especificado.
	* Para `setJars`, especifique o local em que o artefato jar será criado. Geralmente, é `<Your IntelliJ project directory>\out<project name>_DefaultArtifact\default_artifact.jar`.


11. No `RemoteClusterDebugging` da classe, clique com botão direito do mouse na palavra-chave `test` e selecione **Criar Configuração de RemoteClusterDebugging**.

	![Criar configuração remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

12. Na caixa de diálogo, forneça um nome para a configuração e selecione o **Tipo de teste** como **Nome do teste**. Deixe todos os outros valores como padrão, clique em **Aplicar** e clique em **OK**.

	![Criar configuração remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)



13. Agora você deve ver uma lista suspensa de configuração **Execução Remota** na barra de menus.

	![Criar configuração remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## Etapa 5: Executar o aplicativo no modo de depuração

1. No projeto IntelliJ IDEA, abra `SparkSample.scala` e crie um ponto de interrupção ao lado de 'val rdd1'. No menu pop-up para a criação de um ponto de interrupção, selecione **linha na função executeJob**.

	![Adicionar um ponto de interrupção](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)

2. Clique no botão **Execução de Depuração** ao lado da lista suspensa de configuração **Execução Remota** para iniciar a execução do aplicativo.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)

3. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Depurador** no painel inferior.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)

4. Clique no ícone (**+**) para adicionar uma inspeção, como mostrado na imagem abaixo.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

	Aqui, como o aplicativo foi interrompido antes da criação da variável `rdd1`, usando essa inspeção conseguimos ver quais eram as cinco primeiras linhas da variável `rdd`. Pressione **ENTER**.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

	O que você vê na imagem acima é que, em tempo de execução, você poderia consultar terabytes de dados e de depuração à medida que o aplicativo progride. Por exemplo, na saída mostrada na imagem acima, você pode ver que a primeira linha da saída é um cabeçalho. Com base nisso, você pode modificar o código do aplicativo para ignorar a linha de cabeçalho, se necessário.

5. Agora você pode clicar no ícone **Retomar Programa** para prosseguir com o execução do aplicativo.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)

6. Se o aplicativo for concluído com êxito, você verá uma saída semelhante à seguinte.

	![Executar o programa no modo de depuração](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

 

## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões

* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->