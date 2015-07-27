<properties 
	pageTitle="Provisionar um cluster do Spark no HDInsight e usar o Spark SQL do Zeppelin e do Jupyter para análise interativa | Azure" 
	description="Instruções passo a passo sobre como provisionar rapidamente um cluster do Apache Spark no HDInsight e depois usar o Spark SQL dos blocos de anotações do Zeppelin e Jupyter para executar consultas interativas." 
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
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Início rápido: provisionar o Apache Spark no HDInsight e executar consultas interativas usando SQL do Spark

Saiba como provisionar um cluster do Apache Spark no HDInsight usando a opção Criação rápida e, em seguida, usando os blocos de anotações [Zeppelin](https://zeppelin.incubator.apache.org) e [Jupyter](https://jupyter.org), com base na web, para executar consultas interativas no cluster do Spark.


   ![Introdução ao uso do Apache Spark no HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.GetStartedFlow.Spark.png "Introdução ao tutorial de uso do Apache Spark no HDInsight. Etapas ilustradas: criar uma conta de armazenamento; provisionar um cluster; executar instruções do Spark SQL")

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Criar uma conta do Armazenamento do Azure

Ao provisionar um cluster HDInsight no HDInsight, você especifica uma conta de armazenamento do Azure. Um contêiner de armazenamento de blob específico dessa conta é designado como o sistema de arquivos padrão. Por padrão, o cluster HDInsight é provisionado no mesmo datacenter da conta de armazenamento especificada por você. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].


**Para criar uma conta do Armazenamento do Azure**

1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Portal do Azure, onde você pode usar a Criação rápida para configurar uma nova conta de armazenamento.](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.StorageAccount.QuickCreate.png "Portal do Azure, onde você pode usar a Criação rápida para configurar uma nova conta de armazenamento.")

>[AZURE.NOTE]Certifique-se de criar sua conta de armazenamento em um local compatível com o cluster.

Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página. Anote a **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA** – qualquer uma das chaves funciona). Você precisará delas mais tarde no tutorial. Para obter mais informações, consulte [Como criar contas de armazenamento][azure-create-storageaccount].
	
##<a name="provision"></a>Provisionar um cluster HDInsight Spark

Nesta seção, você provisiona um cluster HDInsight versão 3.2, que se baseia no Spark versão 1.3.1. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]As etapas neste artigo criam um cluster do Apache Spark no HDInsight usando configurações básicas. Para obter informações sobre outras definições de configuração do cluster (como usar armazenamento adicional, uma rede virtual do Azure ou um metastore para Hive), consulte [Provisionar clusters do HDInsight usando opções personalizadas](hdinsight-apache-spark-provision-clusters.md).


**Para provisionar um cluster do Spark**

1. Entre no [Portal do Azure][azure-management-portal]. 

2. Clique em **NOVO** no canto inferior esquerdo e insira os valores conforme mostrado na imagem.

	![Criar um cluster do Spark no HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.QuickCreateCluster.png "Criar um cluster do Spark no HDInsight")


##<a name="zeppelin"></a>Executar consultas interativas do Spark SQL usando um bloco de anotações do Zeppelin

Depois de ter provisionado um cluster, você pode usar um bloco de anotações Zeppelin baseado na web para executar consultas interativas no cluster do Spark HDInsight. Nesta seção, usaremos um arquivo de dados de exemplo (hvac.csv) disponível por padrão no cluster para executar algumas consultas interativas do Spark SQL.

>[AZURE.NOTE]O bloco de anotações que você criar seguindo as instruções abaixo também está disponível por padrão no cluster. Depois que você tiver iniciado o Zeppelin, você encontrará este bloco de anotações com o nome **Tutorial HVAC do Zeppelin**.

1. Inicie o bloco de anotações do Zeppelin. Selecione o cluster do Spark recém-criado no portal do Azure e, da barra de tarefas do portal na parte inferior, clique em **Zeppelin Notebook**. Quando solicitado, insira as credenciais para o cluster. Siga as instruções na página que é aberta para iniciar o bloco de anotações.

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Notebook** e, em seguida, clique em **Criar nova nota**.

	![Criar um novo bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.CreateNewNote.png "Criar um novo bloco de anotações do Zeppelin")

	Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com nomes começando com **Nota XXXXXXXXX**. Clique em novo bloco de anotações.

3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

	![Status do bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.NewNote.Connected.png "Status do bloco de anotações do Zeppelin")

4. Carregar dados de exemplo em uma tabela temporária. Quando você provisiona um cluster do Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	No parágrafo vazio que é criado por padrão no novo bloco de anotações, cole o trecho a seguir.

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
		
	Pressione **SHIFT + ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o trecho de código. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

	![Criar uma tabela temporária dos dados brutos](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.LoadDataIntoTable.png "Criar uma tabela temporária dos dados brutos")

	Você também pode fornecer um título para cada parágrafo. No canto direito, clique no ícone **Configurações** e clique em **Mostrar título**.

5. Agora você pode executar instruções do SQL Spark na tabela **hvac**. Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas de destino e real de cada prédio em uma determinada data. Pressione **SHIFT + ENTER**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	A instrução **% sql** no início informa ao bloco de anotações para usar o interpretador SQL Spark. Você pode examinar os interpretadores definidos na guia **Interpretador** no cabeçalho do bloco de anotações.

	A captura de tela a seguir mostra o resultado.

	![Executar uma instrução Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery1.png "Executar uma instrução Spark SQL usando o bloco de anotações")

	 Clique nas opções de exibição (realçadas no retângulo) para alternar entre representações diferentes para o mesmo resultado. Clique em **Configurações** para escolher de que é constituída a chave e os valores no resultado. A captura de tela acima usa **buildingID** como a chave e a média de **temp_diff** como o valor.

	
6. Você também pode executar instruções Spark SQL usando variáveis na consulta. O seguinte trecho mostra como definir uma variável, **Temp**, na consulta com os valores possíveis que deseja consultar. Quando você executa primeiro a consulta, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Cole este trecho de código em um novo parágrafo e pressione **SHIFT + ENTER**. A captura de tela a seguir mostra o resultado.

	![Executar uma instrução Spark SQL usando o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery2.png "Executar uma instrução Spark SQL usando o bloco de anotações")

	Em consultas subsequentes, você pode selecionar um novo valor na lista suspensa e executar a consulta novamente. Clique em **Configurações** para escolher de que é constituída a chave e os valores no resultado. A captura de tela acima usa **buildingID** como a chave e a média de **temp_diff** como o valor, e **targettemp** como o grupo.

7. Reinicie o interpretador de SQL do Spark para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

	![Reiniciar o interpretador do Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Zeppelin.Restart.Interpreter.png "Reiniciar o interpretador do Zeppelin")

##<a name="jupyter"></a>Executar consultas do Spark SQL usando um bloco de anotações do Jupyter com base em Python

Você também pode executar consultas do Spark SQL usando o Python. Nesta seção, você pode usar um bloco de anotações do Jupyter para executar consultas do Spark SQL em um cluster do Spark.

>[AZURE.NOTE]O bloco de anotações que você criar seguindo as instruções abaixo também está disponível por padrão no cluster. Depois que você tiver iniciado o Jupyter, você encontrará este bloco de anotações com o nome **HVACTutorial.ipynb**.

1. Inicie o bloco de anotações do Jupyter. Selecione o cluster do Spark no portal do Azure e, na barra de tarefas do portal na parte inferior, clique em **Bloco de notas Jupyter**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.
2. Crie um novo bloco de anotações. Clique em **Novo** e, em seguida, clique em **Python2**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.CreateNotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Forneça um nome para o bloco de anotações](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.Notebook.Name.png "Forneça um nome para o bloco de anotações")

4. Importe os módulos necessários e crie os contextos do Spark e do SQL. Cole o trecho a seguir em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** junto com o título do bloco de anotações. Você também verá um círculo preenchido ao lado do texto **Python 2** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

	 ![Status de um trabalho de bloco de anotações do Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Job.Status.png "Status de um trabalho de bloco de anotações do Jupyter")

4. Carregar dados de exemplo em uma tabela temporária. Quando você provisiona um cluster do Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	Em uma célula vazia, cole o seguinte trecho e pressione **SHIFT + ENTER**. Este trecho de código registra os dados em uma tabela temporária chamada **hvac**.


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

5. Depois que o trabalho for concluído com êxito, o seguinte resultado é exibido.

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

6. Reinicie o kernel para sair do aplicativo. Na barra de menus superior, clique em **Kernel**, clique em **Reiniciar** e, em seguida, clique em **Reiniciar** novamente no prompt.

	![Reinicie o Kernel Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Restart.Kernel.png "Reinicie o Kernel Jupyter")


##<a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Provisionar um Spark no cluster do HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO3-->