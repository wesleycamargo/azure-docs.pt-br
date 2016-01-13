<properties 
	pageTitle="Usar o Apache Spark para criar aplicativos de aprendizado de máquina no HDInsight | Microsoft Azure" 
	description="Instruções passo a passo sobre como usar blocos de anotações com o Apache Spark para criar aplicativos de aprendizado de máquina" 
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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Aprendizado de máquina: análise de previsão nos dados de inspeção de alimentos usando MLlib com Spark no HDInsight (Linux)

> [AZURE.TIP]Este tutorial também está disponível como um notebook Jupyter em um cluster do Spark (Linux) que você pode criar no HDInsight. A experiência do notebook permite executar os trechos de código Python no próprio notebook. Para executar o tutorial de dentro de um notebook, crie um cluster do Spark, inicie um notebook do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e execute o notebook **Aprendizado de máquina do Spark - análise de previsão em dados de inspeção de alimentos usando MLLib.ipynb** na pasta **Python**.


Este artigo demonstra como usar **MLLib**, as bibliotecas de aprendizado de máquina internas do Spark, para executar uma análise de previsão simples em um conjunto de dados aberto. MLLib é uma biblioteca Spark principal que fornece vários utilitários úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

* Classificação

* Regressão

* Clustering

* Modelagem de tópico

* Decomposição de valor singular (SVD) e análise de componente principal (PCA)

* Teste de hipótese e cálculo de estatísticas de exemplo

Este artigo apresenta uma abordagem simples à *classificação* por meio de regressão logística.

## O que são a classificação e regressão logística?

*Classificação*, uma tarefa muito comum de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação para descobrir como atribuir "rótulos" a dados de entrada que você fornece. Por exemplo, você pode pensar em um algoritmo de aprendizado de máquina que aceite informações sobre estoque como entrada e divida o estoque em duas categorias: estoque que você deve vender e estoque que devem ser mantido.

Regressão logística é o algoritmo usado para classificação. A API de regressão logística do Spark é útil para *classificação binária* ou para classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre a regressão logística, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.

## O que estamos tentando fazer neste artigo?

Você usará o Spark para executar alguma análise de previsão em dados de inspeção de alimentos (**Food\_Inspections1.csv**) adquiridos por meio do [portal de dados da cidade de Chicago](https://data.cityofchicago.org/). Esse conjunto de dados contém informações sobre inspeções de alimentos realizadas em Chicago, incluindo informações sobre cada estabelecimento de alimentos inspecionado, as violações encontradas (se houver) e os resultados da inspeção.

Nas etapas a seguir, você desenvolverá um modelo para ver o que é necessário para ser aprovado ou reprovado em uma inspeção de alimentos.

## Comece a desenvolver um aplicativo de aprendizado de máquina usando a MLlib do Spark

1. No [Portal de Visualização do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha do **Painel de Cluster**, clique em **Bloco de anotações Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE]Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **Novo** e, em seguida, clique em **Python 2**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

3. Comece a criar seu aplicativo de aprendizado de máquina. Você deve iniciar com a configuração do ambiente Pyspark. Para fazer isso, coloque o cursor na célula e pressione **SHIFT + ENTER**.


		import pyspark
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		%matplotlib inline
		import matplotlib.pyplot as plt
		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *
		import atexit
		
		sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
		sqlContext = SQLContext(sc)
		atexit.register(lambda: sc.stop())


## Construir um dataframe de entrada

Já temos um SQLContext que podemos usar para realizar transformações em dados estruturados. A primeira tarefa é carregar os dados de exemplo ((**Food\_Inspections1.csv**)) em um *dataframe* SQL do Spark. Os trechos de código a seguir pressupõem que os dados já estejam carregados no contêiner de armazenamento padrão associado ao cluster do Spark.

1. Como os dados brutos estão em um formato CSV, precisamos usar o contexto do Spark para efetuar pull de cada linha do arquivo na memória como texto não estruturado; em seguida, use a biblioteca CSV do Python para analisar cada linha individualmente. 


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasb:///example/data/Food_Inspections1.csv')\
		                .map(csvParse)


2. Agora temos o arquivo CSV como um RDD. Vamos recuperar uma linha de RDD para compreender o esquema dos dados.


		inspections.take(1)


	Você verá algo semelhante ao mostrado a seguir:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. A saída acima nos dá uma ideia do esquema do arquivo de entrada; o arquivo inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados da inspeção e a localização, entre outras informações. Vamos selecionar algumas colunas que serão úteis para nossa análise de previsão e agrupar os resultados como um dataframe.


		schema = StructType([
		        StructField("id", IntegerType(), False), 
		        StructField("name", StringType(), False), 
		        StructField("results", StringType(), False), 
		        StructField("violations", StringType(), True)])
		
		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)

4. Agora temos uma *dataframe*, `df` em que podemos executar nossa análise. Incluímos quatro colunas de interesse no dataframe: **id**, **nome**, **resultados** e **violações**. Vamos obter uma pequena amostra dos dados:


		df.show(5)

	Você verá algo semelhante ao mostrado a seguir:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## Entender os dados

Vamos começar a ter uma ideia do que o nosso conjunto de dados contém. Por exemplo, quais são os diferentes valores na coluna **resultados**?


	df.select('results').distinct().show()

	
Você verá algo semelhante ao mostrado a seguir:

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	+--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    
Uma rápida visualização pode nos ajudar a entender a distribuição desses resultados.

	countResults = df.groupBy('results').count().collect()
	labels = [row.results for row in countResults]
	sizes = [row.count for row in countResults]
	colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


Você verá algo semelhante ao mostrado a seguir:

    
![Saída de resultado](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


Você pode ver que uma inspeção pode ter cinco resultados distintos

* Negócios não localizados 
* Reprovado
* Aprovado
* Aprovar c/condições e
* Fora de negócio 

Vamos desenvolver um modelo que possa adivinhar o resultado de uma inspeção de alimentos, considerando as violações. Uma vez que a regressão logística é um método de classificação binária, faz sentido agrupar os dados em duas categorias: **Reprovado** e **Aprovado**. Um "Passar c/Condições" ainda é uma aprovação, portanto, ao treinar o modelo, consideraremos os dois resultados equivalentes. Dados com os outros resultados ("Negócios não localizados", "Fora Negócio") não são úteis, então iremos removê-los do nosso conjunto de treinamento. Isso deve ser estar ok, já que essas duas categorias compõem uma porcentagem muito pequena dos resultados de qualquer forma.

Vamos prosseguir e converter nosso dataframe existente (`df`) em um novo dataframe em que cada inspeção é representada como um par de violações de rótulo. No nosso caso, um rótulo de `0.0` representa uma falha, um rótulo de `1.0` representa um sucesso e um rótulo de `-1.0` representa alguns resultados diferentes desses dois. Vamos filtrar esses outros resultados ao calcular o novo quadro de dados.


	def labelForResults(s):
	    if s == 'Fail':
	        return 0.0
	    elif s == 'Pass w/ Conditions' or s == 'Pass':
	        return 1.0
	    else:
	        return -1.0
	label = UserDefinedFunction(labelForResults, DoubleType())
	labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


Vamos recuperar uma linha dos dados rotulados para ver sua aparência.


	labeledData.take(1)


Você verá algo semelhante ao mostrado a seguir:

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## Criar um modelo de regressão logística do dataframe de entrada

Nossa tarefa final é converter os dados rotulados para um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística deve ser um conjunto de *pares de vetor de recurso de rótulo*, em que o “vetor de recurso” é um vetor de números que representa o ponto de entrada de alguma forma. Portanto, precisamos de uma maneira de converter a coluna "violações", que é semiestruturada e contém muitos comentários em texto livre, para uma matriz de números reais que uma máquina facilmente entenderia.

Uma abordagem de aprendizado de máquina padrão para processamento de linguagem natural é atribuir a cada palavra distinta um “índice” e então passar um vetor para o algoritmo de aprendizado de máquina, de modo que o valor de cada índice contenha a frequência relativa da palavra na cadeia de texto.

MLLib oferece uma maneira fácil de executar essa operação. Primeiro, iremos "tokenizar" cada cadeia de caracteres de violações para obter as palavras individuais em cada cadeia de caracteres, então usaremos um `HashingTF` para converter cada conjunto de tokens em um vetor de recurso que então poderá ser passado para o algoritmo de regressão logística para construir um modelo. Vamos realizar todas essas etapas de sequência usando um "pipeline".


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## Avaliar o modelo em um conjunto de dados de teste separado

Podemos usar o modelo criado anteriormente para *prever* quais serão os resultados das novas inspeções com base nas violações observadas. Treinamos esse modelo no conjunto de dados **Food\_Inspections1.csv**. Vamos usar um segundo conjunto de dados, **Food\_Inspections2.csv**, para *avaliar* a força desse modelo em dados novos. Esse segundo conjunto de dados (**Food\_Inspections2.csv**) já deve estar no contêiner de armazenamento padrão associado ao cluster.

O trecho de código a seguir cria um novo dataframe, **predictionsDf** que contém a previsão gerada pelo modelo.


	testData = sc.textFile('wasb:///example/data/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
	testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
	predictionsDf = model.transform(testDf)
	predictionsDf.columns


Você verá algo semelhante ao mostrado a seguir:

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------
	
	['id',
     'name',
     'results',
     'violations',
     'words',
     'features',
     'rawPrediction',
     'probability',
     'prediction']

Examine uma das previsões. Execute este trecho de código:

	predictionsDf.take(1)

Você verá a previsão para a primeira entrada no conjunto de dados de teste.

O método `model.transform()` aplicará a mesma transformação para novos dados com o mesmo esquema, e chegará a uma previsão de como classificar os dados. Podemos fazer algumas estatísticas simples para ter uma ideia de quão precisas foram nossas previsões:


	numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
	                                      (prediction = 1 AND (results = 'Pass' OR 
	                                                           results = 'Pass w/ Conditions'))""").count()
	numInspections = predictionsDf.count()
	
	print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
	print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

A saída se parece com o seguinte:

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate


Usar regressão logística com o Spark fornece um modelo preciso da relação entre as descrições de violações em inglês e se um determinado negócio seria aprovado ou reprovado uma inspeção de alimentos. Podemos construir uma visualização final para ajudar a justificar os resultados do teste:

	
	failSuccess = predictionsDf.where("prediction = 0 AND results = 'Fail'").count()
	failFailure = predictionsDf.where("prediction = 0 AND results <> 'Fail'").count()
	passSuccess = predictionsDf.where("prediction = 1 AND results <> 'Fail'").count()
	passFailure = predictionsDf.where("prediction = 1 AND results = 'Fail'").count()
	labels = ['True positive', 'False positive', 'True negative', 'False negative']
	sizes = [failSuccess, failFailure, passSuccess, passFailure]
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


Você deve ver a saída a seguir.

![Saída da previsão](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


Neste gráfico, um resultado "positivo" refere-se a uma reprovação na inspeção de alimentos, enquanto um resultado negativo refere-se a uma aprovação na inspeção. Isso corresponde (aproximadamente) a uma taxa de falsos negativos de 12,6% e um a uma taxa de falsos positivos de 16,0%.

## Fechar o notebook

Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do notebook, clique em **Fechar e Interromper**. Isso desligará e fechará o notebook.


## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark no aprendizado de máquina: use o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Streaming Spark: use o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Extensões

* [Usar blocos de anotações Zeppelin com um cluster do Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o notebook Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_1223_2015-->