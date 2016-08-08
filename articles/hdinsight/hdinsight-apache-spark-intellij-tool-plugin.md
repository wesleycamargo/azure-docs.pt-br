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
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar aplicativos Spark para um cluster do HDInsight Spark Linux

Este artigo oferece diretrizes detalhadas sobre como desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster HDInsight Spark usando o plug-in do HDInsight para IntelliJ IDEA. Você pode usar o plug-in de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

Você também pode acompanhar um video [aqui](https://mix.office.com/watch/1nqkqjt5xonza) para começar.

>[AZURE.IMPORTANT] Essa ferramenta pode ser usada para criar e enviar aplicativos apenas para um cluster HDInsight Spark no Linux.


##Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Este artigo usa a versão 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).

## Instalar o plug-in Ferramentas do HDInsight para IntelliJ IDEA

O plug-in Ferramentas do HDInsight para IntelliJ IDEA está disponível como parte do Kit de Ferramentas do Azure para IntelliJ. Para obter instruções sobre como instalar o Kit de Ferramentas do Azure, veja [Instalando o Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## Executar um aplicativo Scala Spark em um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, selecione **Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Na próxima janela, forneça os detalhes do projeto.

	* Forneça um nome de projeto e o local do projeto.
	* Para **SDK do Projeto**, forneça uma versão de Java superior à 7.
	* Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada. **Não use a versão 2.11.x**. Este exemplo usa a versão **2.10.6**.

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para o **SDK do Spark**, baixe e use o SDK [deste local](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Você também pode ignorar isso e usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), no entanto, certifique-se de ter o repositório maven correto instalado para desenvolver aplicativos Spark. (Por exemplo, você precisará certificar-se de que a parte do Streaming Spark está instalada se estiver usando o Streaming Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 - não use o repositório marcado como Scala 2.11.)

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Clique em **Concluir**.

3. O projeto do Spark criará automaticamente um artefato para você. Para ver o artefato, siga estas etapas.

	1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
	2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para ver o artefato padrão criado.

		![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

	Você também pode criar seu próprio artefato clicando no ícone **+**, realçado na imagem acima.

4. Na caixa de diálogo **Estrutura do Projeto**, clique em **Projeto**. Se o **SDK do Projeto** for definido como 1.8, defina **Nível de linguagem do projeto** como **7 - Diamonds, ARM, multi-catch etc**.

	![Definir o nível de linguagem do projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. Adicione o código-fonte do aplicativo.

	1. No **Gerenciador de Projetos**, clique com o botão direito do mouse em **src**, aponte para **Novo** e clique em **Classe do Scala**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, para **Tipo** escolha **Objeto** e clique em **OK**.

		![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. No arquivo **MyClusterApp.scala**, cole o código a seguir. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sétima coluna no CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasbs:///HVACOut")
			  }

			}

5. Execute o aplicativo em um cluster HDInsight Spark.

	1. No **Explorer de Projetos**, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.

		![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Você receberá uma solicitação para inserir suas credenciais de assinatura do Azure. Na caixa de diálogo **Envio do Spark**, forneça os valores a seguir.

		* Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.

		* Você precisa selecionar um Artefato do projeto IntelliJ ou selecionar uma opção do disco rígido.

		* Na caixa de texto **Nome da classe principal**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), selecione a classe principal no código-fonte do aplicativo e clique em **OK**.

			![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.

		* Depois de fornecer todas as entradas, a caixa de diálogo deve ser semelhante ao seguinte.

			![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Clique em **Enviar**.

	3. A guia **Envio do Spark** na parte inferior da janela deve começar a exibir o progresso. Você também pode interromper o aplicativo clicando no botão vermelho na janela "Envio do Spark".

        ![Resultado do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    Na próxima seção, você aprenderá a acessar a saída do trabalho usando o plug-in do HDInsight para IntelliJ IDEA.


## Acessar e gerenciar clusters HDInsight Spark usando o plug-in do HDInsight para IntelliJ

Você pode executar várias operações usando o plug-in do HDInsight.

### Acessar o contêiner de armazenamento do cluster

1. No menu **Exibir**, aponte para **Janelas de Ferramenta** e clique em **HDInsight Explorer**. Se receber uma solicitação, insira as credenciais para acessar sua assinatura do Azure.

2. Expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.

3. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.

	![Acessar armazenamento de cluster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Clique no nome do contêiner de armazenamento associado ao cluster. No painel à direita, você verá uma pasta chamada **HVACOut**. Clique duas vezes para abrir a pasta e exibir os arquivos **part-***. Abra um desses arquivos para ver a saída do aplicativo.

### Acesse a exibição do trabalho no plug-in

1. No **HDInsight Explorer**, expanda o nome do cluster Spark e clique em **Trabalhos**.

2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Clique no nome do aplicativo para o qual você deseja ver mais detalhes.

	![Acessar a exibição de trabalhos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. As caixas de **Mensagem de Erro**, **Saída do Trabalho**, **Logs de Trabalho do Livy** e **Logs do Driver do Spark** são preenchidas com base no aplicativo selecionado.

4. Você também pode abrir a **Interface do Usuário de Histórico do Spark** e a **Interface do Usuário do YARN** (no nível do aplicativo) clicando nos respectivos botões na parte superior da tela.

### Acessar o Servidor de Histórico do Spark

1. No **HDInsight Explorer**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir a Interface do Usuário de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

2. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código acima, defina o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### Iniciar o portal do Ambari

No **Gerenciador do HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

### Gerenciar assinaturas do Azure

Por padrão, o plug-in do HDInsight lista os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, especifique as assinaturas das quais você deseja acessar o cluster. No **HDInsight Explorer**, clique com o botão direito do mouse no nó raiz **HDInsight** e clique em **Gerenciar Assinaturas**. Na caixa de diálogo, desmarque as caixas de seleção com base na assinatura que você não deseja acessar e clique em **Fechar**. Você também poderá clicar em **Sair** se quiser fazer logoff da sua assinatura do Azure.


## Executar um aplicativo Scala Spark localmente

Você pode usar o plug-in Ferramentas do HDInsight para IntelliJ IDEA para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como o contêiner de armazenamento e podem ser executados e testados localmente.

### Pré-requisito

Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que ocorre devido à ausência do WinUtils.exe no Windows. Para solucionar esse erro, você deve [baixar aqui o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\\WinUtils\\bin**. Em seguida, adicione uma variável de ambiente **HADOOP\_HOME** e defina o valor da variável como **C\\WinUtils**.

### Executar um aplicativo Scala Spark local	 

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Na próxima janela, forneça os detalhes do projeto.

	* Forneça um nome de projeto e o local do projeto.
	* Para **SDK do Projeto**, forneça uma versão de Java superior à 7.
	* Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada. **Não use a versão 2.11.x**. Este exemplo usa a versão **2.10.6**.

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para o **SDK do Spark**, baixe e use o SDK [deste local](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Você também pode ignorar isso e usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), no entanto, certifique-se de ter o repositório maven correto instalado para desenvolver aplicativos Spark. (Por exemplo, você precisará certificar-se de que a parte do Streaming Spark está instalada se estiver usando o Streaming Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 - não use o repositório marcado como Scala 2.11.)

		![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

	* Clique em **Concluir**.

3. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executado localmente em seu computador.

	![Aplicativo Scala local](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  Clique com o botão direito do mouse no aplicativo **LogQuery** e clique em **"Executar ‘LogQuery’"**. Você verá uma saída como esta na guia **Executar** na parte inferior.

	![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## Converter aplicativos IntelliJ IDEA existentes para usar o plug-in de Ferramentas HDInsight

Você também pode converter os aplicativos Spark Scala existentes criados no IntelliJ IDEA para serem compatíveis com o plug-in de Ferramentas do HDInsight. Isso permitirá que você use a ferramenta para enviar os aplicativos a um cluster HDInsight Spark. Faça isso executando as seguintes etapas:

1. Para um aplicativo Scala Spark existente criado usando IntelliJ IDEA, abra o arquivo .iml associado.
2. No nível raiz, você verá um elemento **module** como o seguinte:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Edite o elemento para adicionar `UniqueKey="HDInsightTool"`, de modo que o elemento **module** seja semelhante ao seguinte:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Salve as alterações. Agora, seu aplicativo deve ser compatível com o plug-in de Ferramentas do HDInsight. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up deve ter a opção para **Enviar Aplicativo Spark ao HDInsight**.


## Solucionar problemas

### Erro "Use um tamanho de heap maior" na execução local

No Spark 1.6, se você estiver usando um SDK Java de 32 bits durante a execução local, poderá encontrar os seguintes erros:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
    	at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
    	at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
    	at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
    	at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
    	at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
    	at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
    	at LogQuery$.main(LogQuery.scala:53)
    	at LogQuery.main(LogQuery.scala)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:606)
    	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Isso ocorre simplesmente porque o tamanho do heap não é grande o suficiente para executar o Spark, já que o Spark requer pelo menos 471 MB (você poderá obter mais detalhes em [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081), se desejar). Uma solução simples é usar um SDK do Java de 64 bits. Você também pode alterar as configurações da JVM no IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## Comentários e problemas conhecidos

Atualmente, não há suporte para exibir saídas do Spark diretamente e estamos trabalhando nisso.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar essa ferramenta, fique à vontade para enviar um email no hdivstool em microsoft.com.

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

* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usar o plug-in de Ferramentas do HDInsight para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0727_2016-->