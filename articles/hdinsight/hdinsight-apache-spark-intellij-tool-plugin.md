 <properties 
	pageTitle="Criar aplicativos Scala Spark usando o plug-in do HDInsight para IntelliJ IDEA | Microsoft Azure" 
	description="Saiba como criar um aplicativo Spark autônomos para executar em clusters do HDInsight Spark." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2016" 
	ms.author="nitinme"/>


# Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala (Linux)

Este artigo oferece diretrizes detalhadas sobre como desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster HDInsight Spark usando o plug-in do HDInsight para IntelliJ IDEA. Você pode usar o plug-in de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

>[AZURE.IMPORTANT] Essa ferramenta pode ser usada para criar e enviar aplicativos apenas para um cluster HDInsight Spark no Linux.


**Pré-requisitos**

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Este artigo usa a versão 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/). 


## Instalar o plug-in Scala para IntelliJ IDEA

Se a instalação do IntelliJ IDEA não solicitar para habilitar o plug-in Scala, inicie IntelliJ IDEA e percorra as etapas a seguir para instalar o plug-in:

1. Inicie o IntelliJ IDEA e, na tela de boas-vindas, clique em **Configurar** e, em seguida, clique em **Plug-ins**.

	![Habilitar plug-in do scala](./media/hdinsight-apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

2. Na próxima tela, clique em **Instalar o plugin JetBrains** no canto inferior esquerdo. Na caixa de diálogo **Procurar plug-ins da JetBrains** que é aberta, pesquise por Scala e, em seguida, clique em **Instalar**.

	![Instalar o plug-in do scala](./media/hdinsight-apache-spark-intellij-tool-plugin/install-scala-plugin.png)

3. Após a instalação bem-sucedida do plug-in, você precisará reiniciar o IDE. Pule esta etapa por enquanto.

## Instalar o plug-in Ferramentas do HDInsight para IntelliJ IDEA

1. Se você tiver voltado para a tela de boas-vindas do IntelliJ IDEA, clique em **Configurar** e clique em **Plug-ins** novamente.

2. Na próxima tela, clique em **Procurar Repositórios** no canto inferior esquerdo. Na caixa de diálogo **Procurar Repositórios**, procure **HDInsight**, escolha as **Ferramentas do Microsoft Azure HDInsight para IntelliJ (Visualização)** e clique em **Instalar**.

	![Instalar o plug-in do HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. Quando solicitado, clique no botão **Reiniciar IntelliJ IDEA** para reiniciar o IDE.

## Executar um aplicativo Scala Spark em um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, escolha **Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Na próxima janela, forneça os detalhes do projeto.

	* Forneça um nome de projeto e o local do projeto.
	* Para **SDK do Projeto**, forneça uma versão de Java superior a 7.
	* Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada. **Não use a versão 2.11.x**. Este exemplo usa a versão **2.10.6**.
	
		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para o **SDK do Spark**, baixe e use o SDK [aqui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Clique em **Concluir**.

3. Defina a estrutura do projeto para criar um artefato (pacote jar) que eventualmente conterá o código executado no cluster.

	1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
	2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e clique em **Vazio**.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Insira um nome para o arquivo JAR (por exemplo, **MeuAplicativoDeCluster**). No painel Elementos Disponíveis, clique com o botão direito do mouse em **Saída de compilação de "MeuAplicativoDeCluster"** e clique em **Colocar na Raiz da Saída**.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)
	
	4. Clique em **Aplicar** e clique em **OK**.

4. Adicione o código-fonte do aplicativo.

	1. No **Gerenciador de Projetos**, clique com o botão direito do mouse em **src**, aponte para **Novo** e clique em **Classe do Scala**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, para **Tipo** escolha **Objeto** e clique em **OK**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. No arquivo **MeuAplicativoDeCluster.scala**, cole o código a seguir. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sétima coluna no CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext
			
			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)
			
			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
			
			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
			
			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }
			
			}

5. Execute o aplicativo em um cluster HDInsight Spark.

	1. No **Gerenciador de Projetos**, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.

		![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Você receberá uma solicitação para inserir suas credenciais de assinatura do Azure. Na caixa de diálogo **Envio de Spark**, forneça os valores a seguir.

		![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.

		* A lista suspensa **Artefatos de Compilação** deve listar o nome JAR especificado nas etapas anteriores.

		* Na caixa de texto **Nome da classe principal**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), selecione a classe principal no código fonte do aplicativo e clique em **OK**.

			![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
	
		* Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.
	
		* Clique em **Enviar**.

	3. A guia **Envio do Spark** na parte inferior da janela deve começar a exibir o progresso.

	Na próxima seção, você aprenderá a acessar a saída do trabalho usando o plug-in do HDInsight para IntelliJ IDEA.


## Acessar e gerenciar clusters HDInsight Spark usando o plug-in do HDInsight para IntelliJ

Você pode executar várias operações usando o plug-in do HDInsight.

### Acessar o contêiner de armazenamento do cluster

1. No menu **Exibir**, aponte para **Janelas de Ferramenta** e clique em **Gerenciador do HDInsight**. Se receber uma solicitação, insira as credenciais para acessar sua assinatura do Azure.

2. Expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.

3. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.

	![Acessar armazenamento de cluster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Clique no nome do contêiner de armazenamento associado ao cluster. No painel à direita, você verá uma pasta chamada **HVACOut**. Clique duas vezes para abrir a pasta e ver os arquivos **part-***. Abra um desses arquivos para ver a saída do aplicativo.

### Acessar o Servidor de Histórico do Spark

1. No **Gerenciador do HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir IU do Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

2. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código acima, defina o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Portanto, o nome do aplicativo Spark era **MeuAplicativoDeCluster**.

### Iniciar o portal do Ambari

No **Gerenciador do HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

### Gerenciar assinaturas do Azure

Por padrão, o plug-in do HDInsight lista os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, especifique as assinaturas das quais você deseja acessar o cluster. No **Gerenciador do HDInsight**, clique com o botão direito do mouse no nó raiz **HDInsight** e clique em **Gerenciar Assinaturas**. Na caixa de diálogo, desmarque as caixas de seleção com base na assinatura que você não deseja acessar e clique em **Fechar**. Você também pode clicar em **Sair** se você quiser fazer logoff da sua assinatura do Azure.


## Executar um aplicativo Scala Spark localmente

Você pode usar o plug-in Ferramentas do HDInsight para IntelliJ IDEA para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como o contêiner de armazenamento e podem ser executados e testados localmente.

### Pré-requisito

Durante a execução do aplicativo Spark Scala local em um computador com Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que ocorre devido a à ausência do WinUtils.exe no Windows. Para solucionar esse erro, você deve [baixar aqui o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\\WinUtils\\bin**. Em seguida, adicione uma variável de ambiente **HADOOP\_HOME** e defina o valor da variável como **C\\WinUtils**.

### Executar um aplicativo Scala Spark local	 

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, escolha **Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Na próxima janela, forneça os detalhes do projeto.

	* Forneça um nome de projeto e o local do projeto.
	* Para **SDK do Projeto**, forneça uma versão de Java superior a 7.
	* Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada. **Não use a versão 2.11.x**. Este exemplo usa a versão **2.10.6**.
	
		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para o **SDK do Spark**, baixe e use o SDK [aqui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
	* Clique em **Concluir**.

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-local-project-details.png)

3. Defina a estrutura do projeto para criar um artefato (pacote jar) que eventualmente conterá o código executado no cluster.

	1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
	2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e clique em **Vazio**.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Insira um nome para o arquivo JAR (por exemplo, **MeuAplicativoLocal**). No painel Elementos Disponíveis, clique com o botão direito do mouse em **Saída de compilação de "MeuAplicativoLocal"** e clique em **Colocar na Raiz da Saída**.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-local-jar-2.png)
	
	4. Clique em **Aplicar** e clique em **OK**.

4. Adicione o código-fonte do aplicativo.

	1. No **Gerenciador de Projetos**, clique com o botão direito do mouse em **src**, aponte para **Novo** e clique em **Classe do Scala**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code.png)

	2. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, para **Tipo** escolha **Objeto** e clique em **OK**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code-object.png)

	3. No arquivo **MeuAplicativoLocal.scala**, cole o código a seguir. Esse código lê um arquivo de texto de entrada de exemplo em seu computador e imprime o número de linhas nesse arquivo de texto que contenha os caracteres "a" e "b".


			import org.apache.spark.SparkContext
			import org.apache.spark.SparkContext._
			import org.apache.spark.SparkConf
			
			object MyLocalApp {
			  def main(args: Array[String]) {
			    val logFile = "C:/users/nitinme/Desktop/commands.txt" // Should be some file on your system
			    val conf = new SparkConf().setAppName("MyLocalApp").setMaster("local[2]")
			    val sc = new SparkContext(conf)
			    val logData = sc.textFile(logFile, 2).cache()
			    val numAs = logData.filter(line => line.contains("a")).count()
			    val numBs = logData.filter(line => line.contains("b")).count()
			    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
			  }
			}

5. Execute o aplicativo localmente em sua estação de trabalho. No menu **Executar**, clique em **Executar "MeuAplicativoLocal"**. Você verá uma saída como esta na guia **Executar** na parte inferior.

		...
		...
		Lines with a: 4, Lines with b: 4
		...
		...
		16/02/01 15:04:05 INFO SparkContext: Successfully stopped SparkContext
		16/02/01 15:04:05 INFO ShutdownHookManager: Shutdown hook called
		16/02/01 15:04:05 INFO ShutdownHookManager: Deleting directory C:\Users\nitinme\AppData\Local\Temp\spark-618dee33-45a3-4bce-a8fc-bf85663133b3
		
		Process finished with exit code 0


## Converter aplicativos IntelliJ IDEA existentes para usar o plug-in de Ferramentas HDInsight

Você também pode converter os aplicativos Spark Scala existentes criados no IntelliJ IDEA para serem compatíveis com o plug-in de Ferramentas do HDInsight. Isso permitirá que você use a ferramenta para enviar os aplicativos a um cluster HDInsight Spark. Faça isso executando as seguintes etapas:

1. Para um aplicativo Scala Spark existente criado usando IntelliJ IDEA, abra o arquivo .iml associado.
2. No nível raiz, você verá um elemento **módulo** como o seguinte:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Edite o elemento a fim de adicionar `UniqueKey="HDInsightTool"`, de modo que o elemento **módulo** seja semelhante ao seguinte:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Salve as alterações. Agora, seu aplicativo deve ser compatível com o plug-in de Ferramentas do HDInsight. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up deve ter a opção para **Enviar Aplicativo Spark ao HDInsight**.

## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0309_2016-->