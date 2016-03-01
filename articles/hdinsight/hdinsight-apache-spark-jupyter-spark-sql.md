<properties
	pageTitle="Criar um cluster Spark no HDInsight Linux e usar o Spark SLQ no Jupyter para análise interativa | Microsoft Azure"
	description="Instruções passo a passo sobre como criar rapidamente um cluster Apache Spark no HDInsight e depois usar o Spark SQL nos blocos de anotações do Jupyter para executar consultas interativas."
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


# Introdução: criar cluster Apache Spark no Azure HDInsight (Linux) e executar consultas interativas usando o Spark SQL

Saiba como criar um cluster Apache Spark no HDInsight e usar o bloco de anotações do [Jupyter](https://jupyter.org) para executar consultas interativas do Spark SQL no cluster Spark.

   ![Introdução ao uso do Apache Spark no HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Tuturial de Introdução ao uso do Apache Spark no HDInsight. Etapas ilustradas: criar uma conta de armazenamento; criar um cluster; executar instruções do Spark SQL")

**Pré-requisitos:**

- **Uma assinatura do Azure**. Antes de começar este tutorial, você deve ter uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Um cliente Secure Shell (SSH)**: Linux, Unix, e sistemas OS X fornecem um cliente SSH por meio do comando `ssh`. Para sistemas Windows, é recomendável [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Chaves Secure Shell (SSH) (opcional)**: você pode proteger a conta SSH usada para se conectar ao cluster usando uma senha ou uma chave pública. A senha é o caminho mais rápido, e você deve usar essa opção se quiser criar rapidamente um cluster e executar alguns trabalhos de teste. Usar uma chave é o método seguro, no entanto, exige configuração adicional. Convém usar essa abordagem ao criar um cluster de produção. Neste artigo, nós usamos a abordagem da senha. Para obter instruções sobre como gerar chaves SSH com HDInsight, consulte os seguintes artigos:

	-  Em um computador Linux: [Usar SSH com o HDInsight baseado em Linux (Hadoop) por meio de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Em um computador Windows: [Usar SSH com o HDInsight baseado em Linux (Hadoop) no Windows](hdinsight-hadoop-linux-use-ssh-windows.md).


## Criar um cluster Spark no HDInsight Linux

Nesta seção, você cria um cluster HDInsight versão 3.3, que é baseado no Spark versão 1.5.1. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] As etapas neste artigo criam um cluster do Apache Spark no HDInsight usando definições de configuração básicas. Para obter informações sobre outras definições de configuração do cluster (por exemplo, usar armazenamento adicional, uma rede virtual do Azure ou metastore para Hive), confira [Criar clusters do Spark no HDInsight usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).


**Para criar um cluster Spark**

1. Entre no [Portal de Visualização do Azure](https://ms.portal.azure.com/).

2. Clique em **NOVO**, em **Dados + Análise** e em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, escolha **Hadoop** para o **Tipo de Cluster**. No menu suspenso **Sistema Operacional do Cluster**, escolha **Ubuntu** e escolha a versão do Spark. Uma marca de seleção verde será exibida ao lado do nome do cluster, se ele estiver disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para escolher a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de Recursos** para ver uma lista dos grupos de recursos existentes e escolha no qual criar o cluster. Ou então, você pode clicar em **Criar Novo** e digitar o nome do novo grupo de recursos. Uma marca de seleção verde é exibida para indicar se o novo nome de grupo está disponível.

	> [AZURE.NOTE] Por padrão, essa entrada será um de seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e insira uma senha para o usuário administrador. Você também deve inserir um **Nome de usuário SSH**. Para **Tipo de Autenticação SSH**, clique em **SENHA** e especifique uma senha para o usuário SSH. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "Forneça credenciais de cluster")

    > [AZURE.NOTE] O SSH é usado para acessar remotamente o cluster HDInsight usando uma linha de comando. O nome de usuário e a senha usados aqui serão usados para se conectar ao cluster por meio do SSH. Além disso, o nome de usuário SSH deve ser exclusivo, pois ele cria uma conta de usuário em todos os nós de cluster HDInsight. Veja a seguir alguns dos nomes de conta reservados para uso pelos serviços no cluster e que não podem ser usados como o nome de usuário SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de Dados** para escolher uma fonte de dados existente para o cluster ou criar uma nova. Ao criar um cluster Hadoop no HDInsight, especifique uma conta de armazenamento do Azure. Um contêiner de armazenamento de blobs específico dessa conta é designado como sistema de arquivos padrão, como no sistema de arquivos distribuído Hadoop (HDFS). Por padrão, o cluster HDInsight é criado no mesmo datacenter da conta de armazenamento que você especificou. Para saber mais, confira [Usar o Armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage]

	![Folha de fonte de dados](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de Seleção**: defina essa opção como **De todas as assinaturas** para habilitar a procura de contas de armazenamento em todas as suas assinaturas. Defina essa opção como **Tecla de Acesso** se você desejar inserir o **Nome de Armazenamento** e a **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento/Criar Nova**: clique em **Selecionar conta de armazenamento** para procurar e escolher uma conta de armazenamento existente que deseja associar ao cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde é exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT] Selecionar o local para a fonte de dados padrão também define o local do cluster HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preços do nó.

9. Na folha **Novo Cluster HDInsight**, verifique se a opção **Fixar no Quadro Inicial** está marcada e clique em **Criar**. Isso cria o cluster e adiciona um bloco para esse cluster no Quadro Inicial do seu portal do Azure. O ícone indica que o cluster está sendo criado e será alterado para exibir o ícone de HDInsight após a conclusão da criação.

	| Durante a criação | criação concluída |
	| ------------------ | --------------------- |
	| ![Criando um indicador no quadro inicial](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de criação.

10. Quando a criação estiver concluída, clique no bloco do cluster Spark no Quadro Inicial para iniciar a folha de cluster.



## <a name="jupyter"></a>Executar consultas do Spark SQL usando um bloco de anotações do Jupyter

Nesta seção, você pode usar um bloco de anotações do Jupyter para executar consultas do Spark SQL com relação a um cluster Spark.

1. No [Portal de Visualização do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **Novo** e em **Python2**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

4. Importe os módulos necessários e crie os contextos do Spark e do SQL. Cole o exemplo de código a seguir em uma célula vazia e pressione **SHIFT+ENTER**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		# Create Spark and SQL contexts
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)

	Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo preenchido ao lado do texto **Python 2** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

	 ![Status de um trabalho do bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status de um trabalho do bloco de anotações do Jupyter")

4. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	Em uma célula vazia, cole o exemplo de código a seguir e pressione **SHIFT+ENTER**. Esse exemplo de código registra os dados em uma tabela temporária chamada **hvac**.

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Depois que o trabalho for concluído com êxito, o resultado a seguir será exibido.

		+----------+---------+------+
		|buildingID|temp_diff|  date|
		+----------+---------+------+
		|         4|        8|6/1/13|
		|         3|        2|6/1/13|
		|         7|      -10|6/1/13|
		|        12|        3|6/1/13|
		|         7|        9|6/1/13|
		|         7|        5|6/1/13|
		|         3|       11|6/1/13|
		|         8|       -7|6/1/13|
		|        17|       14|6/1/13|
		|        16|       -3|6/1/13|
		|         8|       -8|6/1/13|
		|         1|       -1|6/1/13|
		|        12|       11|6/1/13|
		|         3|       14|6/1/13|
		|         6|       -4|6/1/13|
		|         1|        4|6/1/13|
		|        19|        4|6/1/13|
		|        19|       12|6/1/13|
		|         9|       -9|6/1/13|
		|        15|      -10|6/1/13|
		+----------+---------+------+


6. Depois de concluir a execução do aplicativo, você deve encerrar o bloco de anotações para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.


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

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

### Problemas conhecidos

* [Problemas conhecidos do Apache Spark no Azure HDInsight (Linux)](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->