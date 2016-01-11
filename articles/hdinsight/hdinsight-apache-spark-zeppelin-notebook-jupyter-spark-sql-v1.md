<properties 
	pageTitle="Criar um cluster Spark no HDInsight e usar o Spark SQL no Zeppelin e Jupyter para análise interativa | Azure" 
	description="Instruções passo a passo sobre como criar rapidamente um cluster Apache Spark no HDInsight e usar o Spark SQL nos blocos de anotações do Zeppelin e Jupyter para executar consultas interativas." 
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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Início Rápido: criar o Apache Spark no HDInsight e executar consultas interativas usando o Spark SQL (Windows)

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Criar o Apache Spark no HDInsight e executar consultas interativas usando o Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

Saiba como criar um cluster Apache Spark no HDInsight com a opção Criação Rápida e usar os blocos de anotações do [Zeppelin](https://zeppelin.incubator.apache.org) e do [Jupyter](https://jupyter.org) baseados na Web para executar consultas interativas do Spark SQL no cluster Spark.


   ![Introdução ao uso do Apache Spark no HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.getstartedflow.png "Tuturial de Introdução ao uso do Apache Spark no HDInsight. Etapas ilustradas: criar uma conta de armazenamento; criar um cluster; executar instruções do Spark SQL")

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Criar uma conta do Armazenamento do Azure

Ao criar um cluster HDInsight no HDInsight, você especifica uma conta de armazenamento do Azure. Um contêiner de armazenamento de blobs específico dessa conta é designado como o sistema de arquivos padrão. Por padrão, o cluster HDInsight é criado no mesmo datacenter da conta de armazenamento que você especificou. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage].


**Para criar uma conta do Armazenamento do Azure**

1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Portal do Azure, no qual você pode usar a Criação rápida para configurar uma nova conta de armazenamento](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.storageaccount.quickcreate.png "Portal do Azure, no qual você pode usar a Criação rápida para configurar uma nova conta de armazenamento")

>[AZURE.NOTE]Certifique-se de criar sua conta de armazenamento em um local compatível com o cluster.

Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página. Anote a **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA** – qualquer uma das chaves funciona). Você precisará delas mais tarde no tutorial. Para obter mais informações, consulte [Como criar contas de armazenamento][azure-create-storageaccount].
	
##Criar um cluster Spark no HDInsight

Nesta seção, você cria um cluster HDInsight versão 3.2, que é baseado no Spark versão 1.3.1. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]As etapas neste artigo criam um cluster do Apache Spark no HDInsight usando definições de configuração básicas. Para obter informações sobre outras definições de configuração do cluster (por exemplo, usar armazenamento adicional, uma rede virtual do Azure ou um metastore para Hive), confira [Provisionar clusters do Apache Spark no HDInsight usando opções personalizadas](hdinsight-apache-spark-provision-clusters.md).


**Para criar um cluster Spark**

1. Entre no [Portal do Azure][azure-management-portal]. 

2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Criar um cluster Spark no HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.quickcreatecluster.png "Criar um cluster Spark no HDInsight")


##<a name="zeppelin"></a>Executar consultas interativas do Spark SQL usando um bloco de anotações do Zeppelin

Depois de criar um cluster, você pode usar um bloco de anotações do Zeppelin baseado na Web para executar consultas interativas do Spark SQL no cluster Spark HDInsight. Nesta seção, usaremos um arquivo de dados de exemplo (hvac.csv) disponível por padrão no cluster para executar algumas consultas interativas do Spark SQL.

>[AZURE.NOTE]O bloco de anotações criado seguindo as instruções abaixo também está disponível por padrão no cluster. Depois de iniciar o Zeppelin, você encontrará esse bloco de anotações com o nome **Tutorial de HVAC do Zeppelin**.

1. No painel esquerdo do [Portal do Azure][azure-management-portal], clique em **HDInsight** e no cluster Spark que você criou. Na página do cluster Spark, no painel inferior, clique em **Bloco de Anotações Zeppelin**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE]Você também pode acessar o Bloco de Anotações Zeppelin de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Notebook** e em **Criar Nova Anotação**.

	![Criar um novo bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Criar um novo bloco de anotações do Zeppelin")

	Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com o nome começando por **Note XXXXXXXXX**. Clique no novo bloco de anotações.

3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações, se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

	![Status do bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Status do bloco de anotações do Zeppelin")

4. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	No parágrafo vazio criado por padrão no novo bloco de anotações, cole o trecho a seguir.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Pressione **SHIFT+ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o trecho. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXCECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

	![Criar uma tabela temporária por meio de dados brutos](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Criar uma tabela temporária por meio de dados brutos")

	Você também pode fornecer um título para cada parágrafo. No canto direito, clique no ícone **Configurações** e em **Mostrar título**.

5. Agora você pode executar instruções do Spark SQL na tabela **hvac**. Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas almejada e real para cada prédio em uma determinada data. Pressione **SHIFT+ENTER**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	A instrução **%sql** no início informa ao bloco de anotações para usar o interpretador Spark SQL. Você pode examinar os interpretadores definidos na guia **Interpretador** no cabeçalho do bloco de anotações.

	A captura de tela a seguir mostra o resultado.

	![Executar uma instrução do Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Executar uma instrução do Spark SQL usando o bloco de anotações")

	 Clique nas opções de exibição (realçadas no retângulo) para alternar entre diferentes representações para o mesmo resultado. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave e a média de **temp\_diff** como o valor.

	
6. Você também pode executar instruções Spark SQL usando variáveis na consulta. O trecho a seguir mostra como definir uma variável, **Temp**, na consulta com os possíveis valores com os quais deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Cole esse trecho em um novo parágrafo e pressione **SHIFT+ENTER**. A captura de tela a seguir mostra o resultado.

	![Executar uma instrução do Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Executar uma instrução do Spark SQL usando o bloco de anotações")

	Em consultas subsequentes, você pode selecionar um novo valor na lista suspensa e executar a consulta novamente. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa **buildingID** como chave, a média de **temp\_diff** como valor e **targettemp** como o grupo.

7. Reinicie o interpretador do SQL Sparks para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

	![Reiniciar o interpretador do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Reiniciar o interpretador do Zeppelin")

##<a name="jupyter"></a>Executar consultas do Spark SQL usando um bloco de anotações do Jupyter

Nesta seção, você pode usar um bloco de anotações do Jupyter para executar consultas do Spark SQL com relação a um cluster Spark.

>[AZURE.NOTE]O bloco de anotações criado seguindo as instruções abaixo também está disponível por padrão no cluster. Depois que tiver iniciado o Jupyter, você encontrará esse bloco de anotações com o nome **HVACTutorial.ipynb**.

1. No painel esquerdo do [Portal do Azure][azure-management-portal], clique em **HDInsight** e no cluster Spark que você criou. Na página do cluster Spark, no painel inferior, clique em **Bloco de Anotações Zeppelin**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE]Você também pode acessar o Bloco de Anotações Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **Novo** e em **Python2**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

4. Importe os módulos necessários e crie os contextos do Spark e do SQL. Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo preenchido ao lado do texto **Python 2** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

	 ![Status de um trabalho do bloco de anotações do Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.job.status.png "Status de um trabalho do bloco de anotações do Jupyter")

4. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	Em uma célula vazia, cole o trecho a seguir e pressione **SHIFT+ENTER**. Esse trecho registra os dados em uma tabela temporária chamada **hvac**.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Depois que o trabalho for concluído com êxito, o seguinte resultado será exibido.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Reinicie o kernel para sair do aplicativo. Na barra de menus superior, clique em **Kernel**, clique em **Reiniciar** e em **Reiniciar** novamente quando solicitado.

	![Reiniciar o kernel do Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.restart.kernel.png "Reiniciar o kernel do Jupyter")


##<a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Provisionar um Spark no cluster do HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1223_2015-->