<properties 
	pageTitle="Instalar o bloco de notas Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight | Microsoft Azure" 
	description="Saiba mais sobre como instalar o bloco de notas Jupyter localmente em seu computador e se conectar a um cluster Apache Spark no Azure HDInsight." 
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
	ms.date="05/25/2016" 
	ms.author="nitinme"/>


# Instalar o bloco de notas Jupyter em seu computador e se conectar ao cluster do Apache Spark no Azure HDInsight (Visualização)

Neste artigo, você aprenderá como instalar blocos de notas Jupyter, com o PySpark personalizado (para o Python) e kernels Spark (para Scala) com a mágica de Spark, e conectar o bloco de notas a um cluster HDInsight.

Mesmo que os blocos de notas Jupyter já estejam disponíveis no cluster Spark no Azure HDInsight, a instalação do Jupyter no seu computador fornece a opção de criar os blocos de notas localmente, de testar o aplicativo em um cluster em execução e de carregar os blocos de notas no cluster. Para carregar os blocos de notas no cluster, você pode carregá-los usando o bloco de notas Jupyter que está em execução ou o cluster, ou salvá-los na pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?


Há três etapas principais envolvidas na instalação do Jupyter e da mágica do Spark em seu computador.

* Instalar bloco de notas Jupyter
* Instalar os kernels PySpark e Spark com a mágica do Spark
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight

Para saber mais sobre os kernels personalizados e a mágica Spark disponível para blocos de notas Jupyter com o cluster HDInsight, confira [Kernels disponíveis para blocos de notas Jupyter com cluster Linux Apache Spark no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

**Pré-requisitos:**

Os pré-requisitos listados aqui não são para a instalação do Jupyter. Eles são para conectar o bloco de notas Jupyter a um cluster HDInsight depois que o bloco de notas está instalado.

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Instalar bloco de notas Jupyter em seu computador

Você deve instalar o Python antes de instalar blocos de notas Jupyter. Python e Jupyter estão disponíveis como parte da [distribuição do Ananconda](https://www.continuum.io/downloads). Quando você instala o Anaconda, na verdade instala uma distribuição do Python. Quando o Anaconda é instalado, você adiciona a instalação do Jupyter executando um comando. Esta seção fornece as instruções que devem ser seguidas.

1. Baixe o [instalador do Anaconda](https://www.continuum.io/downloads) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.

2. Execute o comando a seguir para instalar o Jupyter.

		conda install jupyter

	Para saber mais sobre a instalação do Jupyter, confira [Instalando o Jupyter usando Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## Instalar os kernels e a mágica do Spark

Nesta seção, você instala a mágica do Spark, os kernels PySpark e Spark e configura os kernels para se conectarem a um cluster Apache Spark em execução no Azure HDInsight.

1. Baixe a demonstração pública mais recente da mágica do Spark no [Github](https://github.com/jupyter-incubator/sparkmagic/archive/publicpreview0.5.zip).

2. Descompacte o arquivo baixado em um local no disco. Nestas instruções, nós nos referimos a esse caminho como `$SPARKMAGIC_PATH`.

2. Execute o comando a seguir

		pip install -r $SPARKMAGIC_PATH/requirements.txt  

3. Execute o comando a seguir para instalar a mágica do Spark.

		pip install -e $SPARKMAGIC_PATH

4. Instale os kernels PySpark e Spark. Execute os seguintes comandos:

		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/sparkkernel
		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/pysparkkernel

## Configurar a mágica do Spark para acessar o cluster HDInsight Spark

Nesta seção, você configura a mágica do Spark instalada anteriormente para se conectar a um cluster Apache Spark que já deve ter criado no Azure HDInsight.

1. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Para localizar seu diretório base em qualquer plataforma de sistema operacional, digite os comandos a seguir.

	Inicie o shell do Python. Em uma janela de comando, digite o seguinte:

		python

	No shell do Python, digite o comando a seguir para localizar o diretório base.

		import os
		print os.path.expanduser('~')

2. Navegue até o diretório base e crie uma pasta chamada **.sparkmagic** se ela não existir.

3. Dentro da pasta, crie um arquivo chamado **config** e adicione o trecho JSON a seguir a ele.

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    " base64_password ": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. Substitua **{USERNAME}**, **{CLUSTERDNSNAME}** e **{BASE64ENCODEDPASSWORD}** pelos valores apropriados. Você pode usar vários utilitários em sua linguagem de programação favorita ou online para gerar uma senha codificada em base64 para sua senha real. Um trecho de código Python simples para executar do prompt de comando seria:

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Inicie o Jupyter. Use o comando do prompt de comando a seguir.

		jupyter notebook

6. Verifique se você pode se conectar ao cluster usando o bloco de notas Jupyter e se você pode usar a mágica Spark disponível com os kernels. Execute as seguintes etapas.

	1. Crie um novo bloco de anotações. No canto direito, clique em **Novo**. Você deve ver o kernel padrão **Python2** e dois kernels novos instalados, **PySpark** e **Spark**.

		![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Criar um novo bloco de anotações do Jupyter")

	
		Clique em **PySpark**.


	2. Execute o trecho de código a seguir.

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

	>[AZURE.TIP] Se você quiser atualizar a configuração do bloco de notas para se conectar a um cluster diferente, atualize o config.json com o novo conjunto de valores, conforme mostrado na Etapa 3 acima.




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

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0525_2016-->