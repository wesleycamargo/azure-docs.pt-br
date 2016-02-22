<properties 
	pageTitle="Criar aplicativos scala autônomos para execução em clusters do HDInsight Spark | Microsoft Azure" 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Criar um aplicativo Scala autônomo e executar no cluster HDInsight Spark (Linux)

Esse artigo fornece orientação passo a passo sobre como desenvolver aplicativos Spark autônomos escritos em Scala usando IntelliJ IDEA. O artigo usa o Apache Maven como o sistema de compilação e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA. Em um alto nível, criar um aplicativo Scala no IntelliJ IDEA envolverá as seguintes etapas:


* Usar o Maven como o sistema de compilação.
* Atualizar o arquivo de modelo de objeto do projeto (POM) para resolver as dependências do módulo Spark
* Escreva seu aplicativo em Scala.
* Gere um arquivo jar que pode ser enviado aos clusters HDInsight Spark.
* Execute o aplicativo no cluster Spark usando spark-submit.

**Pré-requisitos**

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Um Java IDE. Este artigo usa IntelliJ IDEA 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/). 


## Instalar o plug-in Scala para IntelliJ IDEA

Se a instalação do IntelliJ IDEA não solicitar para habilitar o plug-in Scala, inicie IntelliJ IDEA e percorra as etapas a seguir para instalar o plug-in:

1. Inicie o IntelliJ IDEA e, na tela de boas-vindas, clique em **Configurar** e, em seguida, clique em **Plug-ins**.

	![Habilitar plug-in do scala](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)

2. Na próxima tela, clique em **Instalar o plugin JetBrains** no canto inferior esquerdo. Na caixa de diálogo **Procurar plug-ins da JetBrains** que é aberta, pesquise por Scala e, em seguida, clique em **Instalar**.

	![Instalar o plug-in do scala](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)

3. Depois que o plug-in for instalado com êxito, clique no **botão Reiniciar IntelliJ IDEA** para reiniciar o IDE.

## Criar um projeto de Scala autônomo

1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar projeto Maven](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)

	* Selecione **Maven** como o tipo de projeto.
	* Especifique um **SDK do projeto**. Clique em Novo e navegue até o diretório de instalação do Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
	* Selecione a opção **Criar do Arquétipo**.
	* Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Isso criará a estrutura de diretório certa e baixará as dependências padrão necessárias para gravar o programa Scala.

2. Forneça os valores relevantes para **GroupId**, **ArtifactId** e **versão**. Clique em **Próximo**.

3. Na caixa de diálogo seguinte, na qual você especificará o diretório inicial do Maven e outras configurações do usuário, aceite os padrões e clique em **Avançar**.

4. Na última caixa de diálogo, especifique um nome de projeto e local e, em seguida, clique em **Concluir**.

5. Exclua o arquivo **MySpec.Scala** em **src\\test\\scala\\com\\microsoft\\spark\\example**. Você não precisa disso para o aplicativo.

6. Se necessário, renomeie os arquivos de origem e de teste padrão. No painel à esquerda, no IntelliJ IDEA, navegue até **src\\main\\scala\\com.microsoft.spark.example**. Clique com botão direito em **App.scala**, clique em **Refatorar**, clique em Renomear Arquivo, forneça o novo nome para o aplicativo na caixa de diálogo e clique em **Refatorar**.

	![Renomear arquivos](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)

7. Nas etapas subsequentes, você irá atualizar o pom.xml para definir as dependências do aplicativo Spark Scala. Para que essas dependências sejam baixadas e resolvidas automaticamente, você deve configurar o Maven adequadamente.

	![Configurar Maven para downloads automáticos](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)

	1. No menu **Arquivo**, clique em **Configurações**.
	2. Na caixa de diálogo **Configurações**, navegue até **Compilação, Execução, Implantação** > **Ferramentas de Compilação** > **Maven** > **Importando**.
	3. Selecione a opção para **Importar projetos Maven automaticamente**.
	4. Clique em **Aplicar** e, em seguida, clique em **OK**. 


8. Atualize o arquivo de origem Scala para incluir o código do aplicativo. Abra e substitua o código de exemplo existente pelo código a seguir e salve as alterações. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sexta coluna e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

		package com.microsoft.spark.example

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
		
		/**
		  * Test IO to wasb
		  */
		object WasbIOTest {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("WASBIOTest")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasb:///HVACout")
		  }
		}


9. Atualize o pom.xml.

	1.  Dentro de `<project><properties>`, adicione o seguinte:

			<scala.version>2.10.4</scala.version>
    		<scala.compat.version>2.10.4</scala.compat.version>
			<scala.binary.version>2.10</scala.binary.version>

	2. Dentro de `<project><dependencies>`, adicione o seguinte:

			<dependency>
		      <groupId>org.apache.spark</groupId>
		      <artifactId>spark-core_${scala.binary.version}</artifactId>
		      <version>1.4.1</version>
		    </dependency>

	Salve as alterações a pom.xml.

10. Crie o arquivo .jar. O IntelliJ IDEA permite a criação de JAR como um artefato de um projeto. Execute as seguintes etapas.

	1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
	2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e, em seguida, clique em **Dos módulos com dependências**.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)

	3. Na caixa de diálogo **Criar JAR de Módulos**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) em relação à **Classe Principal**.

	4. Na caixa de diálogo **Selecionar Classe Principal**, selecione a classe que é exibida por padrão e, em seguida, clique em **OK**.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)

	5. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, clique em **OK**. Isso cria um JAR único com todas as dependências.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)

	6. A guia layout de saída lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**saída de compilação SparkSimpleApp**). Selecione os jars para excluir e, em seguida, clique no ícone **Excluir**.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)

		Certifique-se de que a caixa **Compilar à criação** esteja marcada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Clique em **Aplicar** e em **OK**.

	7. Na barra de menus, clique em **Compilar** e, em seguida, clique em **Criar Projeto**. Você também pode clicar em **Compilar Artefatos** para criar o jar. O jar de saída é criado em **\\out\\artifacts**.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## Executar o aplicativo no cluster do Spark

Para executar o aplicativo no cluster, faça o seguinte:

* **Copie o jar do aplicativo para o blob de armazenamento do Azure** associado ao cluster. Você pode usar [**AzCopy**](storage/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes também que você pode usar para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

* **Use Livy para enviar um trabalho de aplicativo remotamente** para o cluster Spark. Os clusters Spark no HDInsight incluem Livy, que expõe pontos de extremidade REST para enviar remotamente trabalhos do Spark. Para obter mais informações, consulte [Enviar trabalhos do Spark remotamente usando Livy com clusters Spark no HDInsight](hdinsight-apache-spark-livy-rest-interface.md).


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

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->