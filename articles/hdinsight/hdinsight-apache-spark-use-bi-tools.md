<properties 
	pageTitle="Usar as ferramentas de BI com o Apache Spark no HDInsight | Microsoft Azure"
	description="Instruções passo a passo sobre como usar blocos de anotações com o Apache Spark para criar esquemas em dados brutos, salvá-los como tabelas Hive e, em seguida, usar ferramentas de BI na tabela Hive para análise de dados,"
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
	ms.date="07/31/2015"
	ms.author="nitinme"/>


# Usar as ferramentas de BI com o Apache Spark no Azure HDInsight

Saiba como usar o Apache Spark no Azure HDInsight para fazer o seguinte:

* Pegar os dados brutos de exemplo e salvá-los como uma tabela Hive
* Usar ferramentas de BI como Power BI e Tableau para analisar e visualizar os dados.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark. Para obter instruções, consulte [Provisionar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Um computador com o driver ODBC do Microsoft Spark instalado. Você pode instalar o driver [aqui](http://go.microsoft.com/fwlink/?LinkId=616229).
- Ferramentas de BI como [Power BI](http://www.powerbi.com/) ou [Tableau Desktop](http://www.tableau.com/products/desktop). Você pode obter uma assinatura gratuita de visualização do Power BI em [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Salvar dados brutos como uma tabela Hive

Nesta seção, usamos o bloco de anotações do [Jupyter](https://jupyter.org) associado a um cluster do Apache Spark no HDInsight para executar trabalhos que processam os dados brutos de exemplo e os salvam como uma tabela Hive. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que os dados são salvos como uma tabela Hive, na próxima seção, vamos nos conectar à tabela Hive usando ferramentas de BI como Power BI e Tableau.

1. No [Portal de Visualização do Azure](https://ms.portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**. 
 
2. Inicie o bloco de anotações do [Jupyter](https://jupyter.org). Na folha do cluster Spark, clique em **Links Rápidos** e, na folha do **Painel de Cluster**, clique em **Bloco de anotações Jupyter**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

2. Crie um novo bloco de anotações. Clique em **Novo** e, em seguida, clique em **Python 2**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "Fornecer um nome para o bloco de anotações")

4. Importe os módulos necessários e crie os contextos do Spark e do Hive. Cole o trecho a seguir em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** junto com o título do bloco de anotações. Você também verá um círculo preenchido ao lado do texto **Python 2** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

	 ![Status de um trabalho do bloco de anotações do Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "Status de um trabalho do bloco de anotações do Jupyter")

4. Carregar dados de exemplo em uma tabela temporária. Quando você provisiona um cluster do Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\\HdiSamples\\SensorSampleData\\hvac**.

	Em uma célula vazia, cole o seguinte trecho e pressione **SHIFT + ENTER**. Esse trecho de código registra os dados em uma tabela Hive chamada **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

5. Verifique se a tabela foi criada com êxito. Em uma célula vazia no bloco de anotações, copie o seguinte trecho e pressione **SHIFT + ENTER**.

		hiveCtx.sql("SHOW TABLES").show()

	Você verá algo semelhante ao mostrado a seguir:

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	Somente as tabelas que têm falso sob a coluna **isTemporary** são tabelas hive que serão armazenadas no metastore e podem ser acessadas por meio das ferramentas de BI. Neste tutorial, vamos nos conectar à tabela **hvac** recém-criada.

6. Verifique se a tabela contém os dados pretendidos. Em uma célula vazia no bloco de anotações, copie o seguinte trecho e pressione **SHIFT + ENTER**.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. Agora você pode sair do bloco de anotações reiniciando o kernel. Na barra de menus superior, clique em **Kernel**, clique em **Reiniciar** e, em seguida, clique em **Reiniciar** novamente no prompt.

	![Reiniciar o kernel do Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "Reiniciar o kernel do Jupyter")

##<a name="powerbi"></a>Usar o Power BI para analisar os dados da tabela Hive

Depois de salvar os dados como uma tabela Hive, você pode usar o Power BI para conectar aos dados e visualizá-los para criar relatórios, painéis, etc.

1. Entre no [Power BI](http://www.powerbi.com/).

2. Na tela de boas-vindas, clique em **Bancos de dados e mais**.

	![Colocar dados no Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "Colocar dados no Power BI")

3. Na próxima tela, clique em **Spark** e, em seguida, clique em **Conectar**.

4. Na página Spark no Azure HDInsight, forneça os valores para se conectar ao cluster do Spark e, em seguida, clique em **Conectar**.

	![Conectar-se a um cluster do Spark no HDInsight](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "Conectar-se a um cluster do Spark no HDInsight")

	Depois que a conexão é estabelecida, o Power BI inicia a importação de dados do cluster do Spark no HDInsight.

5. O Power BI importa os dados e exibe o novo painel de controle. Também é adicionado um novo conjunto de dados sob o título **Conjuntos de dados**. Clique no bloco Spark no painel para abrir uma planilha para visualizar os dados.

	![Bloco do Spark no painel do Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Bloco do Spark no painel do Power BI")

6. Observe a lista **Campos** nas listas à direita da tabela **hvac** que você criou anteriormente. Expanda a tabela para ver seus campos, conforme você definiu anteriormente no bloco de anotações.

	  ![Listar tabelas Hive](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "Listar tabelas Hive")

7. Crie uma visualização para mostrar a variação entre a temperatura almejada e a temperatura real para cada edifício. Para fazer isso, arraste e solte campo o **BuildingID** em **Eixo** e os campos **ActualTemp**/**TargetTemp** em **Valor**.

	![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "Criar visualizações")

	Além disso, selecione **Mapa da área** (mostrado em vermelho) para visualizar seus dados.

8. Por padrão, a visualização mostra a soma para **ActualTemp** e **TargetTemp**. Para os campos, na lista suspensa, selecione **Média** para obter uma média das temperaturas almejada e real para ambos os prédios.

	![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "Criar visualizações")

9. A visualização de dados deve ser semelhante ao seguinte. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

	![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "Criar visualizações")

10. Clique em **Salvar** no menu superior e forneça um nome de relatório. Você também pode fixar o visual. Quando você fixar uma visualização, ela será armazenada no painel para poder controlar o valor mais recente rapidamente.

	Você pode adicionar quantas visualizações desejar para o mesmo conjunto de dados e fixá-las no painel para um instantâneo dos dados. Além disso, os clusters do Spark no HDInsight são conectados ao Power BI com conexão direta. Isso significa que o Power BI sempre tem as informações mais atualizadas de seu cluster para que você não precise agendar atualizações para o conjunto de dados.

##<a name="tableau"></a>Usar o Tableau Desktop para analisar os dados da tabela Hive
	
1. Inicie o Tableau Desktop. No painel esquerdo, na lista de servidores aos quais se conectar, clique em **Spark SQL**.

2. Na caixa de diálogo de conexão do Spark SQL, forneça os valores conforme mostrado abaixo e, em seguida, clique em **OK**.

	![Conectar-se a um cluster do Spark](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "Conectar-se a um cluster do Spark")

	O menu suspenso de autenticação lista o **Serviço HDInsight do Microsoft** **Azure** como uma opção somente se você tiver instalado o [Driver ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) no computador.

3. Na próxima tela, no menu suspenso **Esquema**, clique no ícone **Localizar** e clique em **padrão**.

	![Localizar esquema](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "Localizar esquema")

4. Para o campo **Tabela**, clique no ícone **Localizar** novamente para listar todas as tabelas do Hive disponíveis no cluster. Você deve ver a tabela **hvac** criada anteriormente usando o bloco de anotações.

	![Localizar tabelas](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "Localizar tabelas")

5. Arraste e solte a tabela para a caixa superior à direita. O Tableau importa os dados e exibe o esquema conforme destacado pela caixa vermelha.

	![Adicionar tabelas ao Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "Adicionar tabelas ao Tableau")

6. Clique na guia **Sheet1** na parte inferior esquerda. Faça uma visualização que mostre as temperaturas almejada e real para todos os edifícios para cada data. Arraste **Data** e **ID do prédio** para **Colunas** e **Temp. real**/**Temp. almejada** para **Linhas**. Em **Marcas**, selecione **Área** para usar uma visualização do mapa de área.

	 ![Adicionar campos para visualização](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "Adicionar campos para visualização")

7. Por padrão, os campos de temperatura são mostrados em agregado. Se você desejar mostrar a temperatura média em vez disso, pode fazer isso na lista suspensa, conforme mostrado abaixo.

	![Obter a média de temperatura](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "Obter a média de temperatura")

8. Você também pode sobrepor um mapa de temperatura a outro para ter uma ideia melhor da diferença entre as temperaturas almejada e real. Mova o mouse para o canto do mapa da área inferior até ver a forma da alça realçada em um círculo vermelho acima. Arraste o mapa para o outro mapa na parte superior e solte o mouse ao ver a forma realçada no retângulo vermelho acima.

	![Mesclar mapas](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "Mesclar mapas")

	 A visualização de dados deve mudar para o seguinte:

	![Visualização](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "Visualização")
	 
9. Clique em **Salvar** para salvar a planilha. Você pode criar painéis e adicionar uma ou mais planilhas a ele.

##<a name="seealso"></a>Consulte também

* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Início rápido: provisionar o Apache Spark no HDInsight e executar consultas interativas usando o Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
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

<!---HONumber=August15_HO8-->