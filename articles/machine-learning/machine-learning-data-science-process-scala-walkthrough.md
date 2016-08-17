<properties
	pageTitle="Ciência de dados usando o Scala com Spark no Azure | Microsoft Azure"
	description="Como usar o Scala para tarefas de aprendizado de máquina supervisionadas com a MLlib (biblioteca de aprendizado de máquina) escalonável do Spark e os pacotes SparkML em um cluster Azure HDInsight Spark."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Ciência de dados usando o Scala com Spark no Azure

Este tópico mostra como usar o Scala para tarefas de aprendizado de máquina supervisionadas com a MLlib (biblioteca de aprendizado de máquina) escalonável do Spark e os pacotes SparkML em um cluster Azure HDInsight Spark. Ele explica as tarefas que constituem o [Processo de ciência de dados](http://aka.ms/datascienceprocess): ingestão e exploração de dados, visualização, engenharia de recursos, modelagem e consumo de modelo. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes. Podemos abordar duas tarefas comuns de aprendizado de máquina supervisionadas:

- Problema de regressão: previsão do valor da gorjeta (US$)
- Classificação binária: previsão da gorjeta ou não (1/0) para uma corrida de táxi

O processo de modelagem requer treinamento e avaliação em conjuntos de dados de teste com métricas precisas e relevantes. Também mostramos como armazenar esses modelos no WASB (armazenamento de blobs do Azure) e como pontuar e avaliar seu desempenho preditivo. Um tópico mais avançado abordado é como os modelos podem ser otimizados usando a validação cruzada e a limpeza de hiperparâmetro. Os dados usados são uma amostra do conjunto de dados de corridas e tarifas de táxi em Nova York de 2013.

[Scala](http://www.scala-lang.org/) é uma linguagem baseada em máquina virtual Java que integra conceitos de linguagem funcional e orientada a objetos. Trata-se de uma linguagem escalonável que é bastante adequada ao processamento distribuído na nuvem e executada em clusters Azure Spark.

O [Spark](http://spark.apache.org/) é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de gráfico e aprendizado de máquina. O pacote [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) fornece um conjunto uniforme de APIs de alto nível criadas com base no DataFrames, que ajudam os usuários a criar e ajustar pipelines práticos de aprendizado de máquina. A [MLlib](http://spark.apache.org/mllib/) é a biblioteca de aprendizado de máquina escalonável do Spark que oferece recursos de modelagem para esse ambiente distribuído.

O [HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) é a oferta do Spark de software livre hospedada no Azure. Ele também inclui suporte para **notebooks Scala do Jupyter** no cluster Spark, que podem executar consultas interativas do Spark SQL para transformar, filtrar e visualizar dados armazenados em WASB (Blobs do Azure). Os trechos de código Scala que fornecem as soluções e mostram as plotagens relevantes para visualizar os dados aqui são executados em notebooks Jupyter instalados nos clusters Spark. As etapas de modelagem nesses tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo.

As etapas de configuração e o código fornecidos neste passo a passo são referentes ao HDInsight 3.4 Spark 1.6. No entanto, o código aqui no [notebook Jupyter para Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb), neste passo a passo, é genérico e deve funcionar em qualquer cluster Spark. As etapas de configuração e gerenciamento do cluster poderão ser ligeiramente diferentes do que é mostrado aqui se você não estiver usando o HDInsight Spark.

> [AZURE.NOTE] Para ver um tópico que mostre como usar o Python, em vez do Scala, para concluir as tarefas em um processo completo de ciência de dados, confira [Visão geral da Ciência de dados usando Spark no Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Pré-requisitos

1\. Antes de começar os tópicos, você precisa ter uma assinatura do Azure. Se ainda não tiver uma, veja o vídeo sobre como [obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Você precisa de um cluster HDInsight 3.4 Spark 1.6 para concluir este passo a passo. Para criar um, veja as instruções fornecidas no artigo sobre como [criar um Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão são especificados no menu **Selecionar Tipo de Cluster**.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Para obter uma descrição dos dados da Corrida de táxi na cidade de Nova York e obter instruções sobre como executar código em um notebook Jupyter no cluster Spark, confira as seções relevantes de [Visão geral da Ciência de dados usando Spark no Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Executar código em um notebook Jupyter no cluster Spark 

É possível iniciar o Notebook do Jupyter no portal do Azure. Encontre o cluster Spark no painel e clique nele para entrar na página de gerenciamento de seu cluster. Em seguida, clique nos **Painéis do Cluster** -> **Notebook Jupyter** para abrir o notebook associado ao cluster Spark.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Você também pode navegar até ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acessar os Notebooks Jupyter. Basta substituir a parte CLUSTERNAME desta URL pelo nome do seu próprio cluster. Você precisará da senha de sua conta de administrador para acessar os blocos de anotações.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Selecione Scala para ver um diretório que contém alguns exemplos de notebooks predefinidos que usam a API do PySpark. O notebook **Exploration Modeling and Scoring using Scala.ipynb (Modelagem e pontuação de exploração usando Scala.ipynb)**, que contém os exemplos de código para esse pacote do tópico Spark, está disponível no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)


Você pode carregar o notebook diretamente do Github para o servidor do notebook Jupyter no seu cluster Spark. Na home page do seu Jupyter, clique no botão **Carregar** na parte direita da tela. Um explorador de arquivos abrirá. Aqui você pode colar a URL do Github (conteúdo bruto) do Notebook Scala e clicar em **Abrir**. O notebook Scala está disponível na seguinte URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## Configuração: predefinir contextos, palavras mágicas do Spark e bibliotecas

### Predefinir contexto do Spark e do Hive

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


Os kernels Spark fornecidos com os notebooks Jupyter têm um contexto predefinido, de modo que não é necessário definir explicitamente contextos do Spark ou do Hive antes de começar a trabalhar com o aplicativo que está em desenvolvimento; eles estão disponíveis por padrão para você. Esses contextos são:

- sc - para SparkContext
- sqlContext - para HiveContext


### Palavras mágicas do Spark

O kernel do Spark fornece algumas "palavras mágicas" predefinidas, que são comandos especiais que podem ser chamados com %%. Há dois comandos que são usados nesses exemplos de código.

- **%%local** Especifica que o código nas linhas posteriores será executado localmente. Deve ser um código Scala válido.
- **%%sql -o <nome da variável>** Executa uma consulta do Hive em sqlContext. Se o parâmetro -o for transmitido, o resultado da consulta será persistido no contexto %%local do Scala como um quadro de dados do Spark.

Para saber mais sobre os kernels para notebooks Jupyter e as "palavras mágicas" predefinidas chamadas com %% (por exemplo, %%local) que eles fornecem, confira [Kernels available for Jupyter notebooks with HDInsight Spark Linux clusters on HDInsight (Kernels disponíveis para notebooks Jupyter com clusters Linux do HDInsight Spark no HDInsight)](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### Importar bibliotecas

Importe o Spark, a MLlib e outras bibliotecas necessárias com o código a seguir.

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## Ingestão de dados
 
A primeira etapa no processo de ciência de dados é ingerir os dados a serem analisados de fontes externas ou sistemas em que eles residem para seu ambiente de modelagem e exploração de dados. Neste passo a passo, lemos um exemplo unificado de 0,1% do arquivo sobre gorjeta e tarifa de táxi (armazenado como um arquivo .tsv). O ambiente de exploração e modelagem de dados é Spark. Esta seção contém o código para concluir a seguinte série de tarefas:

- Definir caminhos de diretório para o armazenamento de dados e modelos
- ler o conjunto de dados (armazenado como um arquivo .tsv)
- Definir um esquema para os dados e limpar os dados
- Criar um quadro de dados limpo e armazená-lo em cache na memória
- Registrá-lo como uma tabela temporária no contexto do SQL
- Consultar a tabela e importar os resultados para um quadro de dados.


### Definir caminhos de diretório para locais de armazenamento no WASB

O Spark pode ler e gravar em um Blob de Armazenamento do Azure (também conhecido como WASB). Portanto, qualquer dado existente armazenado lá pode ser processado usando o Spark, e os resultados podem ser armazenados novamente no WASB.

Para salvar arquivos ou modelos no WASB, o caminho deve ser especificado corretamente. O contêiner padrão anexado ao cluster Spark pode ser referenciado usando um caminho que começa com: “wasb:///”. Outros locais são referenciados por "wasb://".

O exemplo de código a seguir especifica o local dos dados de entrada a serem lidos e o caminho para o blob do Azure que é anexado ao cluster Spark em que o modelo será salvo.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### Importar dados, criar RDD e definir um quadro de dados de acordo com o esquema 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: 8 segundos.


### Consultar tabela e importar os resultados em um quadro de dados. 

Na tabela, veja a tarifa, os dados do passageiro e da gorjeta, filtre dados corrompidos e externos, e imprima várias linhas.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**SAÍDA:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13,5| 1\.0| 2,9| 1\.0
 16,0| 2,0| 3\.4| 1\.0
 10,5| 2,0| 1\.0| 1\.0


## Visualização e exploração de dados 

Depois que os dados forem incluídos no Spark, a próxima etapa no processo de ciência de dados será obter uma compreensão mais profunda dos dados por meio de exploração e visualização. Nesta seção, examinamos os dados do táxi usando consultas SQL e depois importamos os resultados em um quadro de dados a fim de plotar as variáveis de destino e os recursos potenciais para inspeção visual usando o recurso de visualização automática do Jupyter.

### Usar local e palavra mágica do SQL para plotar dados

Por padrão, a saída de qualquer trecho de código executado em um notebook Jupyter é disponibilizada no contexto da sessão que é persistida nos nós de trabalho. Se desejar salvar uma corrida nos nós de trabalho para cada cálculo e, se todos os dados necessários para o cálculo estiverem disponíveis localmente no nó do servidor do Jupyter (que é o nó de cabeçalho), você poderá usar a mágica %%local para executar o trecho de código no servidor do Jupyter.

- A **palavra mágica do SQL (`%%sql`)** O kernel HDInsight Spark dá suporte a consultas do HiveQL fáceis e embutidas no sqlContext. O argumento (-o VARIABLE\_NAME) persiste a saída da consulta SQL como um quadro de dados do Pandas no servidor do Jupyter. Isso significa que ele estará disponível no modo local.
- A **palavra mágica `%%local`** é usada para executar o código localmente no servidor do Jupyter, que é o nó de cabeçalho do cluster HDInsight. Normalmente, você usa a palavra mágica `%%local` em conjunto com a palavra mágica `%%sql` com o parâmetro -o. O parâmetro -o persistiria a saída da consulta SQL localmente e, em seguida, as palavras mágicas de %%local disparariam o próximo conjunto de trechos de código para serem executados localmente na saída das consultas SQL que é persistida localmente

### Consultar os dados usando SQL
Essa consulta recupera as viagens por valor da tarifa, contagem de passageiros e valor da gorjeta.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

No código abaixo, a palavra mágica %%local cria um quadro de dados local, sqlResults, que pode ser usado para plotar usando matplotlib.

> [AZURE.TIP] A palavra mágica local é usada várias vezes neste passo a passo. Se o volume de dados for grande, obtenha a amostra para criar um quadro de dados que possa se ajustar na memória local.

### Plotar os dados

É possível plotar usando o código Python, uma vez que o quadro de dados está no contexto local do quadro de dados Panda.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 O kernel do Spark visualiza automaticamente a saída das consultas SQL (HiveQL) depois que você executa o código. Você tem a opção de escolher entre vários tipos diferentes de visualização:

- Tabela
- Pizza
- Linha
- Área
- Barra

Este é o código para plotar os dados:

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**SAÍDA:**

![Histograma do valor da gorjeta](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Valor de gorjeta por contagem de passageiros](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Valor de gorjeta por valor de tarifa](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## Criando recursos e a preparação de dados para funções de modelagem 

Para usar as funções de modelagem baseadas em árvore no SparkML e na MLlib, o destino e os recursos devem ser preparados para usar uma variedade de técnicas apropriadas, como agrupamento, indexação, codificação one-hot e vetorização. Veja os procedimentos pelos quais passaremos nesta seção:

- Criar um novo recurso **agrupando** horários em buckets de tempo de tráfego
- **Indexação e codificação one-hot** de recursos categóricos
- **Criar amostras e dividir o conjunto de dados** em frações de teste e treinamento
- **Especificar os recursos e a variável de treinamento**, bem como criar treinamentos de codificação indexada e one-hot e entrada de testes denominada RDDs pontuais ou Quadros de Dados
- **Categorizar e vetorizar recursos e destinos** automaticamente para uso como entradas para modelos de AM


### Criar um novo recurso reunindo horários em blocos de tempo de tráfego 

Este código mostra como criar um novo recurso reunindo horários em blocos de tempo de tráfego e como armazenar em cache o quadro de dados resultante na memória. Quando RDDs (Conjuntos de Dados Resilientes Distribuídos) e quadros de dados são usados repetidamente, o armazenamento em cache leva a tempos de execução melhores. Da mesma forma, armazenamos em cache RDDs e quadros de dados em vários estágios no passo a passo.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### Indexação e codificação one-hot de recursos categóricos 

Esta seção mostra como indexar ou codificar recursos categóricos para entrada nas funções de modelagem. As funções de modelagem e previsão de MLlib exigem que recursos com dados de entrada categóricos sejam indexados ou codificados antes do uso.

Dependendo do modelo, você precisa indexá-lo ou codificá-lo de maneiras diferentes. Por exemplo, modelos de regressão linear e logística exigem codificação one-hot, em que, por exemplo, um recurso com três categorias pode ser expandido em três colunas de recursos, em que cada uma contém 0 ou 1, dependendo da categoria de uma observação. A MLlib fornece a função [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para executar a codificação one-hot. Esse codificador mapeia uma coluna de índices de rótulo para uma coluna de vetores binários com, no máximo, um valor único. Essa codificação permite que os algoritmos que esperam recursos valiosos numéricos, como a regressão logística, sejam aplicados em recursos categóricos.

Aqui, transformamos apenas quatro variáveis para mostrar exemplos, que são cadeias de caracteres. Outras variáveis, como dia da semana, representadas por valores numéricos, também podem ser indexadas como variáveis categóricas.

Para indexação, usamos `StringIndexer()` e para codificação one-hot, usamos as funções `OneHotEncoder()` da MLlib. Aqui está o código para indexar e codificar recursos categóricos:


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: quatro segundos.



### Criar amostras e dividir o conjunto de dados em frações de teste e treinamento

Esse código cria uma amostragem aleatória dos dados (o valor de 25% é usado aqui). Embora não seja necessário para este exemplo, devido ao tamanho do conjunto de dados, demonstramos como é possível obter amostras aqui, para que você saiba como usá-lo para seu próprio problema, quando necessário. Quando as amostras são grandes, isso pode economizar um tempo significativo durante o treinamento de modelos. Em seguida, dividimos o exemplo em uma parte de treinamento (75% aqui) e uma parte de teste (25% aqui) para usar na classificação e na modelagem de regressão.

Adicionamos um número aleatório (entre 0 e 1) à linha de alcance (em uma coluna "rand") que pode ser usado para selecionar partições de validação cruzada durante o treinamento.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: dois segundos.


### Especificar os recursos e a variável de treinamento, bem como criar treinamentos de codificação indexada e one-hot e entrada de testes denominada RDDs pontuais ou Quadros de Dados. 

Esta seção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto rotulado e codificá-lo para que ele possa ser usado para treinar e testar a regressão logística de MLlib e outros modelos de classificação. Objetos de ponto rotulados são RDDs (Conjuntos de Dados Resilientes Distribuídos) formatados de uma maneira que é necessária como dados de entrada pela maioria dos algoritmos de ML no MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou esparso, associado a um rótulo/resposta.

Nesse código, especificamos a variável (dependente) de destino e os recursos a serem usados no treinamento e criamos treinamento em codificação one-hot ou indexada e entrada de testes denominada Quadro de Dados ou RDDs pontuais.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: quatro segundos.


### Categorizar e vetorizar recursos e destinos automaticamente para uso como entradas para modelos de AM

Categorize corretamente o destino e os recursos para uso em funções de modelagem baseadas em árvore no SparkML. O código conclui duas tarefas:

1. Cria um destino binário para classificação atribuindo um valor de 0 ou 1 para cada ponto de dados entre 0 e 1 usando um valor de limite de 0,5.
2. Categoriza automaticamente os recursos. Se o número de valores numéricos distintos para qualquer um dos recursos for menor que 32, esse recurso será categorizado.

Veja o código para essas duas tarefas.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## Classificação binária: prever se uma gorjeta será paga ou não

Nesta seção, criamos três tipos de modelo de classificação binária para prever se uma gorjeta será ou não paga:

- Um **modelo de regressão logística** usando a função `LogisticRession()` do SparkML
- Um **modelo de classificação de floresta aleatória** usando a função `RandomForestClassifier()` do SparkML
- Um **modelo de classificação de árvore de aumento gradiente** usando a função `GradientBoostedTrees()` da MLlib

### Criar um modelo de regressão logística

Aqui, criamos um modelo de regressão logística usando a função `LogisticRession()` do SparkML. O código de criação do modelo nesta seção consiste em uma série de etapas:

1. **Modelar os dados de treinamento** com um conjunto de parâmetros
2. **Modelar a avaliação** em um conjunto de dados de teste com métricas
3. **Salvar modelo** no blob para consumo futuro
4. **Pontuar o modelo** em relação aos dados de teste
5. **Plotar resultados** - as curvas ROC

Veja o código para esses procedimentos.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

Em seguida, carregue, pontue e salve os resultados.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**SAÍDA:**

ROC nos dados de teste = 0,9827381497557599


Use o Python em quadros de dados locais Panda para plotar a curva ROC.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()


**SAÍDA:**

![Curva ROC de gorjeta ou não](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### Criar um modelo de classificação de floresta aleatória

Aqui, criamos um modelo de classificação de floresta aleatória usando a função `RandomForestClassifier()` do SparkML e avaliamos o modelo nos dados de teste.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**SAÍDA:**

ROC nos dados de teste = 0,9847103571552683


### Criar um modelo de classificação de árvore de aumento gradiente

Aqui, criamos um modelo de classificação de árvore de aumento gradiente usando a função `GradientBoostedTrees()` da MLlib e avaliamos o modelo nos dados de teste.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**SAÍDA:**

Área sob a curva ROC = 0,9846895479241554


## Regressão: prever valor da gorjeta 

Nesta seção, criamos dois tipos de modelo de regressão para prever o valor da gorjeta:

- Um **modelo de regressão linear regularizada** usando a função `LinearRegression()` do SparkML, salvar o modelo e avaliar o modelo nos dados de teste.
- Um **modelo de regressão de árvore de aumento gradiente** usando a função `GBTRegressor() ` do SparkML


### Criar um modelo de regressão linear regularizada

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: 13 segundos.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**SAÍDA:**

R-sqr nos dados de teste = 0,5960320470835743


Em seguida, veja os resultados do teste como quadro de dados e visualize usando o autoviz do Jupyter e o matplotlib do Python.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

O código cria um quadro de dados local da saída da consulta e plota os dados. A palavra mágica `%%local` cria um quadro de dados local, `sqlResults`, que pode ser usado para plotar com matplotlib.

>[AZURE.NOTE] Essa palavra mágica do Spark é usada várias vezes neste passo a passo. Se a quantidade de dados for grande, você deverá obter uma amostra para criar um quadro de dados que se ajusta na memória local.

Crie plotagens usando matplotlib do Python.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**SAÍDA:**

![Valor da gorjeta: real vs. previsto](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### Criar um modelo de regressão de árvore de aumento gradiente

Aqui, criamos um modelo de regressão de árvore de aumento gradiente usando a função `ML GBTRegressor()` do Spark e avaliamos o modelo nos dados de teste.

As GBTs [(árvores com aumento gradiente)](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) são conjuntos de árvores de decisão. As GBTs treinam árvores de decisão iterativamente para minimizar uma função de perda. As GBTs são usadas para regressão e classificação e podem lidar com recursos categóricos, não exigem o dimensionamento de recursos e podem capturar não linearidades e interações de recursos. Elas também podem ser usadas em uma configuração de classificação multiclasse.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**SAÍDA:**

R-sqr do teste é: 0,7655383534596654



## Utilitários de modelagem avançada para otimização

Nesta seção, mostramos como usar os utilitários do AM que são frequentemente usados para otimização do modelo. Especificamente, mostraremos três diferentes maneiras de otimizar os modelos do AM usando a limpeza de parâmetro e a validação cruzada:

1\. Dividir os dados em conjuntos de treinamento e validação, otimizar o modelo usando a limpeza de hiperparâmetro em um conjunto de treinamento e a avaliação em um conjunto de validação (Regressão Linear)

2\. Otimizar o modelo usando a validação cruzada e a limpeza de hiperparâmetro com a função CrossValidator do SparkML (Classificação Binária)

3\. Otimizar o modelo usando o código personalizado de validação cruzada e limpeza de parâmetro para utilizar qualquer função do AM e o conjunto de parâmetros (Regressão Linear)


A **CV (validação cruzada)** é uma técnica que avalia como um modelo treinado em um conjunto de dados conhecido será generalizado para prever os recursos dos conjuntos de dados nos quais ele não foi treinado. A ideia geral por trás dessa técnica é que um modelo é treinado em um conjunto de dados conhecidos em e, em seguida, a precisão de suas previsões é testada em relação a um conjunto de dados independente. Uma implementação comum usada aqui é dividir um conjunto de dados em partições K e, em seguida, treinar o modelo em um estilo round-robin em todas, exceto uma das partições.

A **otimização do hiperparâmetro** é o problema de escolher um conjunto de hiperparâmetros para um algoritmo de aprendizado, normalmente com o objetivo de otimizar uma medida de desempenho do algoritmo em um conjunto de dados independente. Os **hiperparâmetros** são valores que devem ser especificados fora do procedimento de treinamento do modelo. As suposições sobre esses valores podem afetar a flexibilidade e a precisão dos modelos. As árvores de decisão têm hiperparâmetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. As SVMs (Máquinas de Vetor de Suporte) exigem a configuração de um termo de penalidade de classificação incorreta.

Uma maneira comum de executar a otimização de hiperparâmetro usada aqui é uma pesquisa de grade ou um **limpeza de parâmetro**. Isso consiste em realizar uma pesquisa detalhada pelos valores de um subconjunto especificado do espaço do hiperparâmetro para um algoritmo de aprendizado. A validação cruzada pode fornecer uma métrica de desempenho para classificar os resultados ideais produzidos pelo algoritmo de pesquisa de grade. A CV usada com a limpeza do hiperparâmetro limita os problemas como sobreajuste de um modelo de dados de treinamento para que o modelo mantenha a capacidade de se aplicar ao conjunto de dados geral do qual os dados de treinamento foram extraídos.

### Otimizar o modelo de regressão linear com a limpeza de hiperparâmetro

Divida os dados em conjuntos de treinamento e validação, otimize o modelo usando a limpeza de hiperparâmetro no conjunto de treinamento e a avaliação no conjunto de validação (Regressão Linear)

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**SAÍDA:**

R-sqr do teste é: 0,6226484708501209


### Otimizar o modelo de classificação binária usando a limpeza de hiperparâmetro e a validação cruzada

Aqui, mostramos como otimizar um modelo de classificação binária usando a limpeza de hiperparâmetro e a validação cruzada. Isso usa a função CrossValidator do SparkML.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SAÍDA:**

Tempo necessário para executar a célula acima: 33 segundos.


### Otimizar o modelo de regressão linear usando código personalizado de validação cruzada e limpeza de parâmetro

Aqui, otimizamos o modelo usando código personalizado e identificamos os melhores parâmetros do modelo usando o critério de precisão mais alta. Em seguida, criamos o modelo final, avaliamos o modelo nos dados de teste e salvamos o modelo no armazenamento de blobs. Por fim, carregamos o modelo, pontuamos os dados de teste e avaliamos sua precisão.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**SAÍDA:**

Tempo necessário para executar a célula acima: 61 segundos.


## Consumir modelos do AM criados no Spark automaticamente com o Scala

O procedimento usando código Scala para carregar e pontuar automaticamente novos conjuntos de dados com modelos de AM criados no Spark e salvos nos blobs do Azure foi fornecido no tópico [Pontuar modelos de aprendizado de máquina criados no Spark](machine-learning-data-science-spark-model-consumption.md). Os usuários podem seguir as instruções fornecidas nele e simplesmente substituir o código Python pelo código Scala fornecido acima a fim de permitir o consumo automatizado.

## O que vem a seguir

Para obter uma visão geral dos tópicos que explicam as tarefas que compõem o processo de ciência de dados no Azure, confira [Processo de Ciência de Dados de Equipe](http://aka.ms/datascienceprocess).

Para obter uma descrição dos outros passo a passos, cada um deles demonstrando as etapas no Processo de Ciência de Dados de Equipe para **cenários específicos** e ilustrando como combinar nuvem, ferramentas locais e serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente, confira [Passo a passos do processo de Ciência de Dados de Equipe](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0803_2016-->