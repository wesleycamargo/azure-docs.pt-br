<properties 
	pageTitle="Kernels disponíveis com notebooks Jupyter em clusters HDInsight Spark no Linux | Microsoft Azure" 
	description="Saiba mais sobre os kernels do notebook do Jupyter disponíveis com o cluster Spark no HDInsight Linux." 
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


# Kernels disponíveis para notebooks Jupyter com clusters do Spark no HDInsight (Linux)

O cluster do Apache Spark no HDInsight (Linux) inclui blocos de anotações do Jupyter que você pode usar para testar seus aplicativos. Por padrão, o notebook do Jupyter vem com um kernel **Python2**. Os clusters HDInsight Spark fornecem dois kernels adicionais que você pode usar com o notebook Jupyter. Estes são:

1. **Spark** (para aplicativos escritos em Scala)
2. **PySpark** (para aplicativos escritos em Python)

Neste artigo, você aprenderá como usar esses kernels e quais são os benefícios de usá-los.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Como usar os kernels? 

1. No [Portal de Visualização do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo notebook com os novos kernels. Clique em **Novo** e, em seguida, clique em **Pyspark** ou **Spark**. Você deve usar o kernel Spark para aplicativos Scala e o kernel PySpark para aplicativos Python.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Criar um novo bloco de anotações do Jupyter")

3. Isso deve abrir um novo notebook com o kernel selecionado.

## Por que devo usar os novos kernels?

Há alguns benefícios em usar os novo kernels.

1. Com o kernel **Python2** padrão, você precisa definir os contextos Spark, SQL ou Hive antes de começar a trabalhar com o aplicativo que está desenvolvendo. Se você usar os novos kernels (**Spark** ou **PySpark**), esses contextos estarão disponíveis para você por padrão. Esses contextos são:

	* **sc** - para o contexto do Spark
	* **sqlContext** - para o contexto SQL
	* **hiveContext** - para o contexto Hive


	Portanto, você não precisa executar instruções como as seguintes para definir os contextos:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	Em vez disso, pode usar os contextos predefinidos diretamente em seu aplicativo.
	
2. Você pode usar diretamente as mágicas **sql %** e **hive %** para usar as consultas SQL ou Hive, respectivamente. Portanto, algo assim funcionaria diretamente de fábrica, sem quaisquer instruções de código de orientação.

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## Considerações ao usar os novos kernels

Seja qual for o kernel que você use (Python2, PySpark ou Spark), deixar os notebooks em execução consumirá os recursos de cluster. Com o notebook Python2, uma vez que você criará o contexto explicitamente, você poderá também eliminar esses contextos quando sair do aplicativo.

No entanto, nos kernels PySpark e Spark, uma vez que os contextos são predefinidos, você não pode explicitamente eliminar o contexto também. Portanto, se você simplesmente sair do notebook, o contexto poderá ainda estar em execução, usando os recursos de cluster. Uma boa prática com kernels PySpark e Spark seria usar a opção **Fechar e Interromper** opção do menu **Arquivo** do notebook. Isso elimina o contexto e encerra o notebook.


## Mostre-me alguns exemplos

Quando você abrir um notebook Jupyter, verá duas pastas disponíveis no nível raiz.

* A pasta **Python** tem notebooks de amostra que usam o kernel **Python2** padrão.
* A pasta **Scala** tem notebooks de amostra que usam o novo kernel **Spark**.

Você pode abrir o mesmo notebook (por exemplo, **LEIAME PRIMEIRO – Aprenda as noções básicas do Spark no HDInsight**) das duas pastas para ver como o notebook Python2 sempre começa com a configuração dos contextos necessários, enquanto o notebook Spark apenas usa os contextos predefinidos.

## Comentários

Os novos kernels estão em um estágio muito emergente e evoluirão com o longo do tempo. Isso também pode significar que as APIs podem mudar à medida que esses kernels amadurecem. Agradecemos o envio quaisquer comentários que você tenha ao usar esses novos kernels. Isso será muito útil na formação da versão final desses kernels. Você pode deixar seus comentários/feedback na seção **Comentários** no final deste artigo.


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

<!---HONumber=AcomDC_0211_2016-->