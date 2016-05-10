<properties
	pageTitle="Modelagem e exploração de dados avançados com o Spark | Microsoft Azure"
	description="TBD."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="deguhath;bradsev" />

# Modelagem e exploração de dados avançados com o Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este passo-a-passo usa HDInsight Spark para executar tarefas de exploração de dados, classificação binária e modelagem de regressão usando validação cruzada e otimização de hiperparâmetro para treinar os modelos em uma amostra do conjunto de dados de corridas e tarifas de Nova York de 2013. Ele o orienta ao longo das etapas do [Processo de Ciência de Dados](http://aka.ms/datascienceprocess), de ponta a ponta, usando um cluster HDInsight Spark para processamento e blobs do Azure para armazenar os dados e os modelos. O processo explora e visualiza os dados transferidos de um Blob de Armazenamento do Azure e prepara os dados para criar modelos preditivos. Python foi usado para codificar a solução e mostrar os gráficos relevantes. Esses modelos são compilados usando o kit de ferramentas Spark MLlib para executar tarefas de classificação binária e modelagem de regressão.

- A tarefa de **classificação binária** consiste em prever se uma gorjeta é paga ou não pela corrida. 
- A tarefa de **regressão** consiste em prever o valor da gorjeta com base em outros recursos de gorjeta. 

As etapas de modelagem também contêm código que mostra como treinar, avaliar e salvar cada tipo de modelo. O tópico aborda alguns dos mesmos conteúdos que o tópico [Data exploration and modeling with Spark](machine-learning-data-science-spark-data-exploration-modeling.md) (Modelagem e exploração de dados com Spark), mas é considerado “avançado” porque ele também usa a validação cruzada em conjunto com a limpeza do hiperparâmetro para treinar de forma ideal os modelos de regressão e classificação precisos.

**CV (Validação cruzada)** é uma técnica que avalia quão bem um modelo treinado em um conjunto de dados conhecido será generalizado para prever os recursos dos conjuntos de dados nos quais ele não foi treinado. A ideia geral por trás dessa técnica é que um modelo é treinado em um conjunto de dados conhecidos em e, em seguida, a precisão de suas previsões é testada em relação a um conjunto de dados independente. Uma implementação comum usada aqui é dividir um conjunto de dados em partições K e, em seguida, treinar o modelo em um estilo round-robin em todas, exceto uma das partições.

**Otimização do hiperparâmetro** é o problema de escolher um conjunto de hiperparâmetros para um algoritmo de aprendizado, normalmente com o objetivo de otimizar uma medida de desempenho do algoritmo em um conjunto de dados independente. **Hiperparâmetros** são valores que devem ser especificados fora do procedimento de treinamento do modelo. As suposições sobre esses valores podem afetar a flexibilidade e a precisão dos modelos. As árvores de decisão têm hiperparâmetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. As SVMs (Máquinas de Vetor de Suporte) exigem a configuração de um termo de penalidade de classificação incorreta.

Uma maneira comum de executar a otimização de hiperparâmetro usada aqui é uma pesquisa de grade ou um **limpeza de parâmetro**. Isso consiste em realizar uma pesquisa detalhada pelos valores de um subconjunto especificado do espaço do hiperparâmetro para um algoritmo de aprendizado. A validação cruzada pode fornecer uma métrica de desempenho para classificar os resultados ideais produzidos pelo algoritmo de pesquisa de grade. A CV usada com a limpeza do hiperparâmetro limita os problemas como sobreajuste de um modelo de dados de treinamento para que o modelo mantenha a capacidade de se aplicar ao conjunto de dados geral do qual os dados de treinamento foram extraídos.

Os modelos que usamos incluem regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes:

- [Regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo de regressão linear que usa um método SGD (Stochastic Gradient Descent) para otimização e dimensionamento de recursos para prever os valores das gorjetas pagas. 
- [Regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou regressão "logit" é um modelo de regressão que pode ser usado quando a variável dependente é categórica para fazer a classificação de dados. LBFGS é um algoritmo de otimização quase Newton que aproxima o algoritmo BFGS (Broyden–Fletcher–Goldfarb–Shanno) usando uma quantidade limitada de memória do computador e que é amplamente usado no aprendizado de máquina.
- [Florestas aleatórias](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são conjuntos de árvores de decisão. Elas combinam várias árvores de decisão para reduzir o risco de superajuste. As florestas aleatórias são usadas para regressão e classificação e podem lidar com recursos categóricos e estender-se à configuração de classificação multiclasse, não precisam de dimensionamento de recursos e podem capturar não linearidades e interações de recursos. As florestas aleatórias são um dos modelos de aprendizado de máquina com maior taxa de sucesso para classificação e regressão.
- [GBTs (árvores com aumento gradiente)](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) são conjuntos de árvores de decisão. As GBTs treinam árvores de decisão iterativamente para minimizar uma função de perda. As GBTs são usadas para regressão e classificação e podem lidar com recursos categóricos, não exigem o dimensionamento de recursos e podem capturar não linearidades e interações de recursos. Elas também podem ser usadas em uma configuração de classificação multiclasse.

Os exemplos de modelagem usando CV e limpeza de hiperparâmetro são mostrados para o problema de classificação binária. Exemplos mais simples (sem limpezas de parâmetro) são apresentados no tópico principal para tarefas de regressão. Mas no apêndice, a validação usando a rede elástica para regressão linear e a CV com a limpeza de parâmetros usando a regressão de floresta aleatória também são apresentadas. A **rede elástica** é um método de regressão regularizado para ajustar os modelos de regressão linear que combina linearmente as métricas L1 e L2 como penalidades dos métodos [laço](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) e [ressalto](https://en.wikipedia.org/wiki/Tikhonov_regularization).



>[AZURE.NOTE] Embora o kit de ferramentas Spark MLlib tenha sido projetado para trabalhar com grandes conjuntos de dados, para fins de demonstração de suas funcionalidades de modelagem, uma amostra relativamente pequena (cerca de 30 Mb usando 170 mil linhas, cerca de 0,1% do conjunto de dados original NYC) foi usada por conveniência. O exercício fornecido aqui é executado com eficiência em um cluster HDInsight com dois nós de trabalho (em aproximadamente 10 minutos). O mesmo código, com pequenas modificações, pode ser usado para processar conjuntos de dados maiores, com as modificações apropriadas para armazenar dados na memória em cache ou alterar o tamanho do cluster.


## Pré-requisitos

Você precisa de uma conta do Azure e um cluster HDInsight Spark, versão Spark 1.5.2 (HDI 3.3), para iniciar este passo-a-passo. Confira os requisitos na [Overview of Data Science using Spark on Azure HDInsight](machine-learning-data-science-spark-overview.md) (Visão geral de ciência de dados usando o Spark no Azure HDInsight) para obter uma descrição dos dados NYC 2013 Taxi usados aqui e para obter instruções sobre como executar código de um bloco de anotações do Jupyter no cluster Spark. O bloco de anotações **machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** que contém os exemplos de código deste tópico está disponível no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Instalação: locais de armazenamento, bibliotecas e contexto Spark

O Spark pode ler e gravar em um Blob de Armazenamento do Azure (também conhecido como WASB). Portanto, qualquer dado existente armazenado lá pode ser processado usando o Spark, e os resultados podem ser armazenados novamente no WASB.

Para salvar arquivos ou modelos no WASB, o caminho deve ser especificado corretamente. O contêiner padrão anexado ao cluster Spark pode ser referenciado usando um caminho que começa com: "wasb///". Outros locais são referenciados por "wasb://".

### Definir caminhos de diretório para locais de armazenamento no WASB

O exemplo de código a seguir especifica o local dos dados a serem lidos e o caminho do diretório de armazenamento de modelo em que a saída do modelo será salva.

	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

	# PRINT START TIME
	import datetime
	datetime.datetime.now()

**SAÍDA**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)


### Importar bibliotecas necessárias e definir o contexto do Spark 

Defina o contexto do Spark e importe as bibliotecas necessárias com o código a seguir.

	# LOAD PYSPARK LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	%matplotlib inline
	import matplotlib
	import matplotlib.pyplot as plt
	#matplotlib.style.use('ggplot')
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**SAÍDA**

4

## Ingestão de dados: 

Esta seção contém o código para uma série de tarefas necessárias para ingerir a amostra de dados a ser modelada. Leia uma amostra de 0,1% do arquivo de corridas e tarifas de táxi associado (armazenado como um arquivo .tsv), formate e limpe os dados, armazene um quadro de dados em cache na memória e registre-o como uma tabela temporária no contexto do SQL.

A primeira etapa no processo de ciência de dados é ingerir os dados a serem analisados de fontes externas ou sistemas em que residem para seu ambiente de modelagem e exploração de dados. Esse ambiente é o Spark neste passo a passo. Esta seção contém o código para concluir uma série de tarefas:

- ingerir a amostra de dados a ser modelada
- ler o conjunto de dados (armazenado como um arquivo .tsv)
- formatar e limpar os dados
- criar e armazenar objetos em cache (RDDs ou quadros de dados) na memória
- registrá-lo como uma tabela temporária no contexto do SQL.

Aqui está o código para ingestão de dados.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_train_file = sc.textFile(taxi_train_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_train_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
	taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	
	    
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

Tempo necessário para executar a célula acima: 17,73 segundos


## Visualização e exploração de dados 

Depois que os dados forem incluídos no Spark, a próxima etapa no processo de ciência de dados será obter uma compreensão mais profunda dos dados por meio de exploração e visualização. Nesta seção, podemos examinar os dados de táxi usando consultas SQL e plotar as variáveis de destino e os recursos em potencial para inspeção visual. Especificamente, plotamos a frequência das contagens de passageiros em corridas de táxi, a frequência de gorjetas e como as gorjetas variam de acordo com o valor e o tipo de pagamento.

### Plotar um histograma de frequências de contagens de passageiros na amostra de corridas de táxi

O código usa uma consulta a SQL para amostragem dos dados e converte os resultados em um quadro de dados Pandas para plotagem.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# SQL SQUERY
	sqlStatement = """
	    SELECT passenger_count, COUNT(*) as trip_counts 
	    FROM taxi_train 
	    WHERE passenger_count > 0 and passenger_count < 7
	    GROUP BY passenger_count 
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	#CONVERT TO PANDAS DATA-FRAMES FOR PLOTTING IN PYTHON
	resultsPDDF = sqlResults.toPandas()
	
	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	x_labels = resultsPDDF['passenger_count'].values
	fig = resultsPDDF[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Counts of trips by passenger count')
	fig.set_xlabel('Passenger counts')
	fig.set_ylabel('Trip counts')
	plt.show()

**SAÍDA**

![Frequência de corridas por contagem de passageiros](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)


### Plote um histograma de valores de gorjetas e como o valor das gorjetas varia pelas tarifas e contagens de passageiros.

O código usa uma consulta a SQL para amostragem dos dados e converte os resultados em um quadro de dados Pandas para plotagem.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SQL SQUERY
	sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	# CONVERT TO PANDAS DATA-FRAME FOR PLOTTING IN PYTHON
	resultsPDDF= sqlResults.toPandas()
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount ($) by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount ($)')
	ax.set_xlabel('Fare Amount')
	ax.set_ylabel('Tip Amount')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Saída:**

![Distribuição de valores de gorjetas](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Valor de gorjeta por contagem de passageiros](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Valor de gorjeta por valor de tarifa](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

Tempo necessário para executar a célula acima: 10,42 segundos


## Engenharia de recursos, transformação e preparação de dados para a modelagem

Esta seção descreve e fornece o código para os procedimentos usados para preparar dados para uso na modelagem ML. Ela mostra como realizar as seguintes tarefas:

- Criar um novo recurso reunindo horários em blocos de tempo de tráfego
- Indexar e fazer a codificação one-hot dos recursos categóricos
- Criar objetos de ponto rotulado para entrada em funções de ML
- Criar uma subamostragem aleatória dos dados e dividi-la em conjuntos de treinamento e teste
- Dimensionamento de recursos
- Armazenar objetos em cache na memória


### Criar um novo recurso reunindo horários em blocos de tempo de tráfego

Este código mostra como criar um novo recurso reunindo horários em blocos de tempo de tráfego e como armazenar em cache o quadro de dados resultante na memória. Quando RDDs (Conjuntos de Dados Resilientes Distribuídos) e quadros de dados são usados repetidamente, o armazenamento em cache leva a tempos de execução melhores. Da mesma forma, armazenamos em cache RDDs e quadros de dados em vários estágios no passo a passo.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**SAÍDA**

126050


### Indexar e fazer a codificação one-hot dos recursos categóricos

Esta seção mostra como indexar ou codificar recursos categóricos para entrada nas funções de modelagem. As funções de modelagem e previsão de MLlib exigem que recursos com dados de entrada categóricos sejam indexados ou codificados antes do uso.

Dependendo do modelo, você precisa indexá-lo ou codificá-lo de maneiras diferentes. Por exemplo, modelos de regressão linear e logística exigem codificação one-hot, em que, por exemplo, um recurso com três categorias pode ser expandido em três colunas de recursos, em que cada uma contém 0 ou 1, dependendo da categoria de uma observação. O MLlib fornece a função [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para executar a codificação one-hot. Esse codificador mapeia uma coluna de índices de rótulo para uma coluna de vetores binários com, no máximo, um valor único. Essa codificação permite que os algoritmos que esperam recursos valiosos numéricos, como a regressão logística, sejam aplicados em recursos categóricos.

Aqui está o código para indexar e codificar recursos categóricos:


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
	
	# INDEX AND ENCODE VENDOR_ID
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_train_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

Tempo necessário para executar a célula acima: 1,22 segundos


### Criar objetos de ponto rotulado para entrada em funções de ML

Esta seção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto rotulado e codificá-lo para que ele possa ser usado para treinar e testar a regressão logística de MLlib e outros modelos de classificação. Objetos de ponto rotulados são RDDs (Conjuntos de Dados Resilientes Distribuídos) formatados de uma maneira que é necessária como dados de entrada pela maioria dos algoritmos de ML no MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou esparso, associado a um rótulo/resposta.


	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
	                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt


	# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt



### Criar uma subamostragem aleatória dos dados e dividi-la em conjuntos de treinamento e teste

Esse código cria uma amostragem aleatória dos dados (o valor de 25% é usado aqui). Embora não seja necessário para este exemplo, devido ao tamanho do conjunto de dados, demonstramos como é possível obter amostras aqui, para que você saiba como usá-lo para seu próprio problema, quando necessário. Quando as amostras são grandes, isso pode economizar um tempo significativo durante o treinamento de modelos. Em seguida, dividimos o exemplo em uma parte de treinamento (75% aqui) e uma parte de teste (25% aqui) para usar na classificação e na modelagem de regressão.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	from pyspark.sql.functions import rand
	
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
	# CACHE TRAIN AND TEST DATA
	trainData.cache()
	testData.cache()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
	indexedTESTbinary = testData.map(parseRowIndexingBinary)
	oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
	oneHotTESTbinary = testData.map(parseRowOneHotBinary)
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg = trainData.map(parseRowIndexingRegression)
	indexedTESTreg = testData.map(parseRowIndexingRegression)
	oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
	oneHotTESTreg = testData.map(parseRowOneHotRegression)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

Tempo necessário para executar a célula acima: 0,4 segundos


### Dimensionamento de recursos

O dimensionamento de recursos, também conhecido como normalização de dados, faz com que recursos com valores amplamente distribuídos não tenham peso excessivo na função objetiva. O código para o dimensionamento de recursos usa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para dimensionar os recursos para variância de unidade. Ele é fornecido por MLlib para uso na regressão linear com SGD (Stochastic Gradient Descent), um algoritmo popular de treinamento de uma grande variedade de outros modelos de aprendizado de máquina, como regressões regularizadas ou SVM (máquinas de vetor de suporte).


>[AZURE.NOTE] Observamos que o algoritmo LinearRegressionWithSGD é sensível ao dimensionamento de recursos.


Aqui está o código para escalar as variáveis para uso com o algoritmo SGD linear regularizado.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	
	# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
	label = oneHotTRAINreg.map(lambda x: x.label)
	features = oneHotTRAINreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	label = oneHotTESTreg.map(lambda x: x.label)
	features = oneHotTESTreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

Tempo necessário para executar a célula acima: 7,33 segundos

### Armazenar objetos em cache na memória

O tempo necessário para treinamento e teste dos algoritmos de ML pode ser reduzido armazenando-se em cache os objetos de quadro de dados de entrada usados para classificação, regressão e recursos dimensionados.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.cache()
	indexedTESTbinary.cache()
	oneHotTRAINbinary.cache()
	oneHotTESTbinary.cache()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.cache()
	indexedTESTreg.cache()
	oneHotTRAINreg.cache()
	oneHotTESTreg.cache()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.cache()
	oneHotTESTregScaled.cache()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Saída:**

Tempo necessário para executar a célula acima: 0,11 segundos


## Prever se uma gorjeta é paga ou não com modelos de classificação binária

Esta seção mostra como usar três modelos para a tarefa de classificação binária de prever se uma gorjeta é paga ou não por uma corrida de táxi. Os modelos apresentados são:

- Regressão logística 
- Floresta aleatória
- Árvores de Ampliação de Gradiente

Cada seção de código de compilação de modelo será dividida em etapas:

1. Dados de **treinamento de modelo** com um conjunto de parâmetros
2. **Avaliação de modelo** em um conjunto de dados de teste com métricas
3. **Salvamento do modelo** no blob para consumo futuro

Mostraremos como fazer a CV (validação cruzada) com a limpeza de parâmetro de duas maneiras:

1. Usando o código personalizado **genérico** que pode ser aplicado a qualquer algoritmo MLlib e quaisquer conjuntos de parâmetros em um algoritmo. 
1. Usando a **função de pipeline CrossValidator do pySpark**. Observe que, embora seja conveniente, com base em nossa experiência, o CrossValidator tem algumas limitações para o Spark 1.5.0: 

	- Os modelos de pipeline não podem ser salvos/persistidos para consumo futuro.
	- Não pode ser usado para todos os parâmetros em um modelo.
	- Não pode ser usado para todos os algoritmos MLlib.


### Limpeza de hiperparâmetro e validação cruzada genérica usadas com o algoritmo de regressão logística para classificação binária

O código nesta seção mostra como treinar, avaliar e salvar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é paga ou não por uma corrida no conjunto de dados de corridas e tarifas de táxi de Nova York. O modelo é treinado usando a CV (validação cruzada) e a limpeza de hiperparâmetro implementada com o código personalizado que pode ser aplicado a qualquer um dos algoritmos de aprendizado na MLlib.


>[AZURE.NOTE] A execução desse código personalizado de CV pode levar vários minutos.


	# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	
	# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
	from sklearn.grid_search import ParameterGrid
	grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
	paramGrid = list(ParameterGrid(grid))
	numModels = len(paramGrid)
	
	# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
	nFolds = 3;
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	# BEGIN CV WITH PARAMETER SWEEP
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowOneHotBinary)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        regt = paramGrid[j]['regType']
	        regp = paramGrid[j]['regParam']
	        iters = paramGrid[j]['iterations']
	        tol = paramGrid[j]['tolerance']
	        # Train logistic regression model with hypermarameter set
	        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
	                                                  regParam=regp, tolerance = tol, intercept=True)
	        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
	        metric = validMetrics.areaUnderROC
	        metricSum[j] += metric
	
	avgAcc = metricSum / nFolds;
	bestParam = paramGrid[np.argmax(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	    
	# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
	logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
	                                              iterations=bestParam['iterations'], 
	                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
	                                              intercept=True)
	
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

Tempo necessário para executar a célula acima: 160,47 segundos


**Avaliar o modelo de classificação binária com métricas standard e plotar a curva ROC**

O código nesta seção mostra como avaliar um modelo de regressão logística em um conjunto dados de teste, incluindo uma plotagem da curva ROC.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	
	# INSTANTIATE METRICS OBJECT
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# AREA UNDER PRECISION-RECALL CURVE
	print("Area under PR = %s" % metrics.areaUnderPR)

	# AREA UNDER ROC CURVE
	print("Area under ROC = %s" % metrics.areaUnderROC)
	metrics = MulticlassMetrics(predictionAndLabels)

	# OVERALL STATISTICS
	precision = metrics.precision()
	recall = metrics.recall()
	f1Score = metrics.fMeasure()
	print("Summary Stats")
	print("Precision = %s" % precision)
	print("Recall = %s" % recall)
	print("F1 Score = %s" % f1Score)
	
	
	# CREATE A PANDAS DATA-FRAME AND PLOT ROC-CURVE, FROM PREDICTED PROBS AND LABELS                                     
	logitBest.clearThreshold() # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	predictionAndLabelsDF = predictionAndLabels.toDF()
	test_predictions = predictionAndLabelsDF.toPandas()
	predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
	
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
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
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

Área sob PR = 0,985319161941

Área sob ROC = 0,983511076103

Estatísticas de resumo

Precisão = 0,984187223276

Recuperação = 0,984187223276

Pontuação F1 = 0,984187223276

![Curva ROC de regressão logística de abordagem genérica](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

Tempo necessário para executar a célula acima: 5,02 segundos


**Persistir o modelo em um blob para consumo futuro**

O código nesta seção mostra como salvar o modelo de regressão logística para consumo.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	# PERSIST MODEL
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitBest.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**SAÍDA**

Tempo necessário para executar a célula acima: 9,96 segundos


### Usar a função de pipeline CrossValidator da MLlib com o modelo LogisticRegression (regressão elástica)

O código nesta seção mostra como treinar, avaliar e salvar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é paga ou não por uma corrida no conjunto de dados de corridas e tarifas de táxi de Nova York. O modelo é treinado usando a CV (validação cruzada) e a limpeza de hiperparâmetro implementada com a função de pipeline CrossValidator da MLlib para CV com limpeza de parâmetro.


>[AZURE.NOTE] A execução desse código de CV da MLlib pode levar vários minutos.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.classification import LogisticRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import BinaryClassificationEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	from sklearn.metrics import roc_curve,auc
	
	# DEFINE ALGORITHM / MODEL
	lr = LogisticRegression()
	
	# DEFINE GRID PARAMETERS
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build()
	
	# DEFINE CV WITH PARAMETER SWEEP
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=BinaryClassificationEvaluator(),
	                    numFolds=3)
	
	# CONVERT TO DATA-FRAME: THIS WILL NOT RUN ON RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
	
	# TRAIN WITH CROSS-VALIDATION
	cv_model = cv.fit(trainDataFrame)
	

	## PREDICT AND EVALUATE ON TEST DATA-SET

	# USE TEST DATASET FOR PREDICTION
	testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
	test_predictions = cv_model.transform(testDataFrame)
	
	# CONVERT RTO PANDAS DATA-FRAME FOR CALCULATING AND PLOTTING ROC CURVE
	predictions_pddf = test_predictions.toPandas()
	predictions_pddf.dtypes
	prob = [x[1] for x in predictions_pddf["probability"]]
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
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
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA**

![Curva ROC de regressão logística usando CrossValidator da MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

Tempo necessário para executar a célula acima: 118,25 segundos


### Classificação de floresta aleatória

O código nesta seção mostra como treinar, avaliar e salvar uma regressão de floresta aleatória que prevê se uma gorjeta é paga ou não por uma corrida no conjunto de dados de corridas e tarifas de táxi de Nova York.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# TRAIN RANDOMFOREST MODEL
	rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
	                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                       numTrees=25, featureSubsetStrategy="auto",
	                                       impurity='gini', maxDepth=5, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp;
	dirfilename = modelDir + rfclassificationfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

Área sob ROC = 0,985240932843

Tempo necessário para executar a célula acima: 22,9 segundos


### Classificação de árvores de ampliação de gradiente

O código nesta seção mostra como treinar, avaliar e salvar um modelo de árvores de ampliação de gradiente que prevê se uma gorjeta é paga ou não por uma corrida no conjunto de dados de corridas e tarifas de táxi de Nova York.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                numIterations=10)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# Area under ROC curve
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN A BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
	dirfilename = modelDir + btclassificationfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

Área sob ROC = 0,985240932843

Tempo necessário para executar a célula acima: 22,41 segundos


## Prever valores de gorjetas com modelos de regressão (não usando CV)

Esta seção mostra como usar três modelos para a tarefa de regressão de prever o valor da gorjeta paga por uma corrida de táxi com base em outros recursos de gorjeta. Os modelos apresentados são:

- Regressão linear regularizada
- Floresta aleatória
- Árvores de Ampliação de Gradiente

Esses modelos foram descritos na introdução. Cada seção de código de compilação de modelo será dividida em etapas:

1. Dados de **treinamento de modelo** com um conjunto de parâmetros
2. **Avaliação de modelo** em um conjunto de dados de teste com métricas
3. **Salvamento do modelo** no blob para consumo futuro   


>OBSERVAÇÃO DO AZURE: a validação cruzada não é usada com os três modelos de regressão nesta seção. Mas um exemplo que mostra como usar a CV com a rede elástica para a regressão linear é fornecido no Apêndice desse tópico.


>OBSERVAÇÃO DO AZURE: em nossa experiência, pode haver problemas com convergência de modelos LinearRegressionWithSGD, e os parâmetros precisam ser alterados/otimizados cuidadosamente para a obtenção de um modelo válido. O dimensionamento de variáveis ajuda significativamente com a convergência. A regressão de rede elástica, mostrada no Apêndice desse tópico, também pode ser usado para melhorar a convergência.


### Regressão linear com SGD

O código nesta seção mostra como usar recursos dimensionados para treinar uma regressão linear que usa SGD (Stochastic Gradient Descent) para otimização e como pontuar, avaliar e salvar o modelo no WASB (Armazenamento de Blobs do Azure).


	# LINEAR REGRESSION WITH SGD 

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
	from pyspark.mllib.evaluation import RegressionMetrics
	from scipy import stats
	
	# USE SCALED FEATURES TO TRAIN MODEL
	linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)
	
	# SCORE ON SCALED TEST DATA-SET & EVALUATE
	predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
	testMetrics = RegressionMetrics(predictionAndLabels)
	
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

RMSE = 1,29395294535

R-sqr = 0,588405443258

Tempo necessário para executar a célula acima: 36,14 segundos


### Regressão de Floresta Aleatória

O código nesta seção mostra como treinar, avaliar e salvar um modelo de floresta aleatória que prevê o valor da gorjeta para os dados de corridas de táxi de Nova York.


>[AZURE.NOTE] A validação cruzada com a limpeza de parâmetro usando código personalizado é fornecida no apêndice.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	# UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp;
	dirfilename = modelDir + rfregressionfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA**

RMSE = 0,962262172157

R-sqr = 0,69142848223

Tempo necessário para executar a célula acima: 29,3 segundos


### Regressão de árvores de ampliação de gradiente

O código nesta seção mostra como treinar, avaliar e salvar um modelo de árvores de ampliação de gradiente que prevê o valor da gorjeta para os dados de corridas de táxi de Nova York.


	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	# EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions= sqlContext.createDataFrame(predictionAndLabels)
	test_predictions_pddf = test_predictions.toPandas()
	
	ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
	plt.axis([-1, 20, -1, 20])
	plt.show(ax)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

RMSE = 0,962160568829

R-sqr = 0,717354800581

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## Apêndice: Tarefas de regressão adicionais usando a validação cruzada com limpezas de parâmetro

Este apêndice contém o código que mostra como fazer a CV usando rede elástica para regressão linear e como fazer a CV com a limpeza de parâmetro usando o código personalizado para a regressão de floresta aleatória.


### Validação cruzada usando a rede elástica para a regressão linear

O código nesta seção mostra como fazer a validação cruzada usando a rede elástica para a regressão linear e como avaliar o modelo em relação aos dados de teste.

	###  CV USING ELASTIC NET FOR LINEAR REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.regression import LinearRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import RegressionEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	
	# Define algo / model
	lr = LinearRegression()
	
	# Define grid parameters
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build() 
	
	# Define pipeline, in this case, simply the modeling (without any transformations etc.)
	pipeline = Pipeline(stages=[lr])
	
	# Define CV with parameter sweep
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=RegressionEvaluator(),
	                    numFolds=3)
	
	# Convert to data-frame, as CrossValidator won't run on RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
	
	# Train with cross-validation
	cv_model = cv.fit(trainDataFrame)
	
	timeend = datetime.datetime.now()
	timeend-timestart
	

	# EVALUATE MODEL ON TEST SET
	testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
	
	# MAKE PREDICTIONS ON TEST DOCUMENTS 
	#THE cvModel USES THE BEST MODEL FOUND (lrModel).
	test_predictions = cv_model.transform(testDataFrame)
	predictions_pddf = test_predictions.toPandas()
	
	corstats = stats.linregress(predictions_pddf['label'],predictions_pddf['prediction'])
	r2 = (corstats[2]*corstats[2])
	print("R-sqr = %s" % r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

R-sqr = 0,594830601664

Tempo necessário para executar a célula acima: 129,51 segundos


### Validação cruzada com limpeza de parâmetro usando o código personalizado para a regressão de floresta aleatória

O código nesta seção mostra como fazer a validação cruzada com a limpeza de parâmetro usando o código personalizado para a regressão de floresta aleatória e como avaliar o modelo em relação aos dados de teste.


	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	from sklearn.grid_search import ParameterGrid
	
	## CREATE PARAMETER GRID
	grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
	paramGrid = list(ParameterGrid(grid))
	
	## SPECIFY LEVELS OF CATEGORICAL VARIBLES
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
	nFolds = 3;
	numModels = len(paramGrid)
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowIndexingRegression)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        maxD = paramGrid[j]['maxDepth']
	        numT = paramGrid[j]['numTrees']
	        # Train logistic regression model with hypermarameter set
	        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=numT, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=maxD, maxBins=32)
	        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
	        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = RegressionMetrics(predictionAndLabels)
	        metric = validMetrics.rootMeanSquaredError
	        metricSum[j] += metric
	
	avgAcc = metricSum/nFolds;
	bestParam = paramGrid[np.argmin(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	        
	## TRAIN FINAL MODL WIHT BEST PARAMETERS
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

	# EVALUATE MODEL ON TEST DATA
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	#PRINT TEST METRICS
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA**

RMSE = 0,990182456723

R-sqr = 0,609523627251

Tempo necessário para executar a célula acima: 72,5 segundos


### Limpar objetos da memória e imprimir os locais de modelo

Use `unpersist()` para excluir objetos armazenados em cache na memória.

	# UNPERSIST OBJECTS CACHED IN MEMORY

	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	trainData.unpersist()
	trainData.unpersist()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.unpersist()
	indexedTESTbinary.unpersist()
	oneHotTRAINbinary.unpersist()
	oneHotTESTbinary.unpersist()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.unpersist()
	indexedTESTreg.unpersist()
	oneHotTRAINreg.unpersist()
	oneHotTESTreg.unpersist()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.unpersist()
	oneHotTESTregScaled.unpersist()

	# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


**SAÍDA**

PythonRDD[119] em RDD em PythonRDD.scala:43

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS\_2016-04-1817\_40\_35.796789"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD\_2016-04-1817\_44\_00.993832"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification\_2016-04-1817\_42\_58.899412"

randomForestRegFileLoc = modelDir + "RandomForestRegression\_2016-04-1817\_44\_27.204734"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification\_2016-04-1817\_43\_16.354770"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression\_2016-04-1817\_44\_46.206262"

## O que vem a seguir?

Agora que criou modelos de regressão e classificação com o Spark MlLib, você está pronto para aprender a classificar e avaliar os modelos.

**Consumo de modelos:** para aprender a classificar e avaliar os modelos de classificação e regressão criados neste tópico, confira [Pontuar e avaliar modelos de aprendizado de máquina criados com Spark](machine-learning-data-science-spark-model-consumption.md).

<!---HONumber=AcomDC_0427_2016-->