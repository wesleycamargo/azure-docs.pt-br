 <properties
	pageTitle="Criar aplicativos Spark Scala usando o plug-in do HDInsight para Eclipse | Microsoft Azure"
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
	ms.date="07/07/2016"
	ms.author="nitinme"/>


# Usar o plug-in de Ferramentas do HDInsight para Eclipse para criar aplicativos Spark para um cluster do HDInsight Spark Linux

Este artigo oferece diretrizes detalhadas sobre como desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster HDInsight Spark usando o plug-in do HDInsight para Eclipse. Você pode usar o plug-in de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

>[AZURE.IMPORTANT] Essa ferramenta pode ser usada para criar e enviar aplicativos apenas para um cluster HDInsight Spark no Linux.


##Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de desenvolvimento Oracle Java versão 7 e 8.
	* O **Java SDK 7** é usado para compilar projetos Spark, pois os clusters são compatíveis com o Java versão 7. Você pode baixar o Java SDK 7 [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
	* O **Java SDK 8** é usado para o tempo de execução do IDE do Eclipse. Você pode baixá-lo [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Eclipse IDE. Este artigo usa o Eclipse Neon. Você pode instalá-lo clicando [aqui](https://www.eclipse.org/downloads/).

* Scala IDE para Eclipse.
	* **Se tiver instalado o Eclipse IDE**, você pode adicionar o plug-in Scala IDE acessando **Ajuda** -> **Instalar Novo Software** e adicionar [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) como fonte de download do plug-in Scala para Eclipse.
	* **Caso não tenha instalado o Eclipse IDE**, você pode instalar o Scala IDE diretamente [deste local](http://scala-ide.org/download/sdk.html). Você pode baixar o arquivo. zip desse link, extraí-lo, navegar até a pasta **/eclipse** e executar o arquivo **eclipse.exe** dela.
	
	>[AZURE.NOTE] As etapas neste documento presumem o uso do Eclipse IDE com o plug-in Scala instalado.

* Spark SDK. Você pode baixá-lo [aqui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Instale o e(fx)clipse de [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## Instalar o plug-in Ferramentas do HDInsight para Eclipse

1. Inicie o Eclipse IDE. Navegue até o local onde você baixou e extraiu o pacote .zip, navegue até a pasta **/eclipse** e clique em **eclipse.exe**. Na tela de boas-vindas, clique em **Ajuda** e, em seguida, clique em **Instalar Novo Software**.

	![Instalar o plug-in do HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-1.png)

2. Na próxima tela, na caixa de texto **Trabalhar com**, digite **http://dl.microsoft.com/eclipse** e pressione **ENTER**. Selecione **Kit de Ferramentas do Azure para Java**, desmarque a caixa de seleção **Entrar em contato com todos os sites de atualização durante a instalação para encontrar o software necessário** e, em seguida, clique em **Próximo**.

	![Instalar o plug-in do HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-2.png)

3. Na caixa de diálogo **Detalhes da Instalação**, verifique os componentes que serão instalados e, em seguida, clique em **Próximo**.

4. Na caixa de diálogo **Verificar Licenças**, aceite os termos da licença e, em seguida, clique em **Concluir**.

5. Depois que a instalação for concluída, você deverá reiniciar o Eclipse. Clique em **Sim** na caixa de diálogo para reinicie o Eclipse.

## Faça logon na sua assinatura do Azure

1. Abra o Azure Explorer. No menu **Janela** do IDE, clique em **Mostrar Exibição** e, em seguida, clique em **Outros**. Na caixa de diálogo que é aberta, expanda **Azure**, clique em **Azure Explorer** e, em seguida, clique em **OK**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. Clique com o botão direito no nó **Azure** do **Azure Explorer** e, em seguida, clique em **Gerenciar Assinaturas**.

3. Na caixa de diálogo **Gerenciar Assinaturas**, clique em **Entrar** e insira suas credenciais do Azure.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. Após o logon, a caixa de diálogo **Gerenciar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **Fechar** na caixa de diálogo.

5. Na guia Azure Explorer, expanda **HDInsight** para ver os clusters HDInsight Spark da sua assinatura.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## Configurar um projeto Spark Scala para um cluster HDInsight Spark

1. No espaço de trabalho do Eclipse IDE, clique em **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.

2. No assistente **Novo Projeto**, expanda **HDInsight**, selecione **Spark no HDInsight (Scala)** e, em seguida, clique em **Próximo**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. Na caixa de diálogo **Novo Projeto do HDInsight Scala**, insira/selecione valores conforme é mostrado na imagem abaixo e clique em **Próximo**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* Insira um nome para o projeto.
	* Na caixa **JRE**, certifique-se de que **Usar um ambiente de execução JRE** esteja definido como **JavaSE-1.7**.
	* O Spark SDK deve ser definido como o local onde você baixou o SDK. O link para o local de download está incluído nos [Pré-requisitos](#prerequisites) anteriormente apresentados neste tópico. Você também pode baixar o SDK no link incluído nesta caixa de diálogo, como é mostrado na imagem acima.

4. Na próxima caixa de diálogo, clique na guia **Bibliotecas** e, em seguida, clique duas vezes em **Biblioteca do Sistema JRE [JavaSE-1.7]**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. Na caixa de diálogo **Editar Biblioteca**, certifique-se de que **Ambiente de Execução** esteja definido como **JavaSE-1.7(jdk1.7.0\_79)**. Se essa opção não estiver disponível, siga as etapas abaixo.

	1. Selecione a opção **JRE Alternativo** e verifique se **JavaSE-1.7(jdk1.7.0\_79)** está disponível.
	2. Caso contrário, clique no botão **JREs Instalados**.

		  ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. Na caixa de diálogo **JREs Instalados**, clique em **Adicionar**.

		  ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. Na caixa de diálogo **Tipo de JRE**, selecione **VM Padrão** e, em seguida, clique em **Próximo**

		  ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. Na caixa de diálogo **Definição de JRE**, clique no diretório e, em seguida, navegue até o local de instalação do JDK 7 e selecione a pasta raiz de **jdk1.7.0\_79**.

		  ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. Clique em **Concluir**. Na caixa de diálogo **JREs Instalados**, selecione o JRE adicionado recentemente e, em seguida, clique em **OK**.

		   ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. O recém-adicionado JRE deve estar listado para **Ambiente de Execução**. Clique em **Concluir**.

	  	   ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. De volta à guia **Bibliotecas**, clique duas vezes em **Contêiner da Biblioteca Scala [2.11.8]**. Na caixa de diálogo **Editar Biblioteca**, selecione **Contêiner Fixo da Biblioteca Scala: 2.10.6**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	Clique em **Concluir** até sair da caixa de diálogo de configurações do projeto.

## Criar um aplicativo Scala para cluster HDInsight Spark

1. No Eclipse IDE aberto, em **Explorador de Pacotes**, expanda o projeto que você criou anteriormente, clique com botão direito em **src**, aponte para **Novo** e, em seguida, clique em **Outros**.

2. Na caixa de diálogo **Selecionar um assistente**, expanda **Assistentes Scala**, clique em **Objeto Scala**, e em **Próximo**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. Na caixa de diálogo **Criar um Novo Arquivo**, insira um nome para o objeto e clique em **Concluir**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. Cole o seguinte código no editor de texto.

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

	1. No **Explorer de Pacotes**, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.

	2. Na caixa de diálogo **Envio do Spark**, forneça os valores a seguir.

		* Para **Nome do Cluster**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.

		* Você precisa selecionar um Artefato do projeto Eclipse ou selecionar uma opção do disco rígido.

		* Na caixa de texto **Nome da classe principal**, digite o nome do objeto especificado no código (confira a imagem abaixo).

			![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.

		* Clique em **Enviar**.

	3. A guia **Envio de Spark** deve começar a exibir o progresso. Você pode interromper o aplicativo clicando no botão vermelho na janela "Envio do Spark". Você também pode exibir os logs para essa execução de aplicativo específica clicando no ícone de globo (indicado pela caixa azul na imagem).

        ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    Na próxima seção, você aprenderá a acessar a saída do trabalho usando o plug-in do HDInsight para Eclipse.


## Acessar e gerenciar clusters HDInsight Spark usando o plug-in do HDInsight para Eclipse

Você pode executar várias operações usando o plug-in do HDInsight.

### Acessar o contêiner de armazenamento do cluster

1. No Azure Explorer, expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.

3. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Clique no nome do contêiner de armazenamento associado ao cluster. No painel à direita, você verá uma pasta chamada **HVACOut**. Clique duas vezes para abrir a pasta e exibir os arquivos **part-***. Abra um desses arquivos para ver a saída do aplicativo.

### Acessar o Servidor de Histórico do Spark

1. No **Azure Explorer**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir a Interface do Usuário de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

2. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código acima, defina o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### Iniciar o portal do Ambari

No **Azure Explorer**, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

### Gerenciar assinaturas do Azure

Por padrão, o plug-in do HDInsight lista os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, especifique as assinaturas das quais você deseja acessar o cluster. No **Azure Explorer**, clique com o botão direito no nó raiz do **Azure** e, em seguida, clique em **Gerenciar Assinaturas**. Na caixa de diálogo, desmarque as caixas de seleção com base na assinatura que você não deseja acessar e clique em **Fechar**. Você também poderá clicar em **Sair** se quiser fazer logoff da sua assinatura do Azure.


## Executar um aplicativo Scala Spark localmente

Você pode usar o plug-in Ferramentas do HDInsight para Eclipse para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como o contêiner de armazenamento e podem ser executados e testados localmente.

### Pré-requisito

Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que ocorre devido à ausência do **WinUtils.exe** no Windows. Para solucionar esse erro, você deve [baixar aqui o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\\WinUtils\\bin**. Em seguida, adicione uma variável de ambiente **HADOOP\_HOME** e defina o valor da variável como **C\\WinUtils**.

### Executar um aplicativo Scala Spark local	 

1. Inicie o Eclipse e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.

	![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* No painel esquerdo, escolha **HDInsight**.
	* No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)**.
	* Clique em **Próximo**.

2. Para fornecer os detalhes do projeto, siga as etapas 3 a 6, como mostrado na seção anterior [Configurar um projeto de aplicativo Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executado localmente em seu computador.

	![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  Clique com botão direito do mouse no aplicativo **LogQuery**, aponte para **Executar como** e clique em **1 Aplicativo Scala**. Você verá uma saída como esta na guia **Console** na parte inferior.

	![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## Comentários e problemas conhecidos

Atualmente, não há suporte para exibir saídas do Spark diretamente e estamos trabalhando nisso.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar essa ferramenta, fique à vontade para enviar um email para hdivstool@microsoft.com.

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

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0706_2016-->