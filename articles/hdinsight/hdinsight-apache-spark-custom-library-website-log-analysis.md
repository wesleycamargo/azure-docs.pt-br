<properties 
	pageTitle="Use bibliotecas personalizadas com um cluster HDInsight Spark para analisar logs do website | Microsoft Azure" 
	description="Use bibliotecas personalizadas com um cluster HDInsight Spark para analisar logs de sites" 
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
	ms.date="04/14/2016" 
	ms.author="nitinme"/>

# Analisar logs do site usando uma biblioteca personalizada com HDInsight Spark no Linux (Preview)

Este notebook demonstra como analisar dados de log usando uma biblioteca personalizada com o Spark no HDInsight. A biblioteca personalizada que usamos é uma biblioteca Python chamada **iislogparser.py**.

> [AZURE.TIP] Este tutorial também está disponível como um notebook Jupyter em um cluster do Spark (Linux) que você pode criar no HDInsight. A experiência do bloco de anotações permite executar os trechos de código Python no próprio bloco de anotações. Para executar o tutorial de dentro de um notebook, crie um cluster Spark, inicie um notebook do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e, em seguida, execute o notebook **Analisar logs com o Spark usando uma library.ipynb personalizada** na pasta **PySpark**.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Salvar dados brutos como RDD

Nesta seção, usamos o notebook [Jupyter](https://jupyter.org) associado a um cluster do Apache Spark no HDInsight para executar trabalhos que processam dados brutos de exemplo e os salvam como uma tabela Hive. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que os dados são salvos como uma tabela Hive, na próxima seção, vamos nos conectar à tabela Hive usando ferramentas de BI como Power BI e Tableau.

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")

3. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

	![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")

4. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos que são obrigatórios para este cenário. Cole o trecho a seguir em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**.


		from pyspark.sql import Row
		from pyspark.sql.types import *


5. Crie um RDD usando os dados de log de exemplo já disponíveis no cluster. Você pode acessar os dados na conta de armazenamento padrão associada ao cluster em **\\HdiSamples\\HdiSamples\\WebsiteLogSampleData\\SampleLog\\909f2b.log**.


		logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Recupere um log de exemplo definido para verificar se a etapa anterior foi concluída com êxito.

		logs.take(5)

	Você deverá ver um resultado semelhante ao seguinte:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[u'#Software: Microsoft Internet Information Services 8.0',
		 u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## Analise os dados de log usando uma biblioteca Python personalizada

7. Na saída acima, as primeiras linhas incluem as informações de cabeçalho, e cada linha restante coincide com o esquema descrito naquele cabeçalho. Analisar esses logs pode ser complicado. Então, usamos uma biblioteca personalizada do Python (**iislogparser.py**) que torna a análise desses logs muito mais fácil. Por padrão, essa biblioteca é incluída com o cluster do Spark no HDInsight em **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

	No entanto, essa biblioteca não está no `PYTHONPATH`, então não podemos usá-la com uma instrução de importação como `import iislogparser`. Para usar essa biblioteca, devemos distribuí-la para todos os nós de trabalho. Execute o trecho de código a seguir.


		sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. O `iislogparser` fornece uma função `parse_log_line` que retorna `None` se uma linha do log for uma linha de cabeçalho, e retorna uma instância da classe `LogLine` se encontrar uma linha do log. Use a classe `LogLine` para extrair somente as linhas do log do RDD:

		def parse_line(l):
		    import iislogparser
		    return iislogparser.parse_log_line(l)
		logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Recupere algumas linhas de log extraídas para verificar se a etapa foi concluída com êxito.

		logLines.take(2)

	A saída deverá ser semelhante a esta:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
 		2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. A classe `LogLine`, por sua vez, tem alguns métodos úteis, como `is_error()`, que retorna se uma entrada de log tem um código de erro. Use isso para calcular o número de erros nas linhas de log extraídas e então registre todos os erros em um arquivo diferente.

		errors = logLines.filter(lambda p: p.is_error())
		numLines = logLines.count()
		numErrors = errors.count()
		print 'There are', numErrors, 'errors and', numLines, 'log entries'
		errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

	Você verá algo semelhante ao mostrado a seguir:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		There are 30 errors and 646 log entries

12. Você também pode usar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se você quiser isolar a causa de solicitações executadas por um longo tempo, talvez queira localizar os arquivos que, em média, levam mais tempo para atender. O trecho a seguir recupera os 25 principais recursos que levaram mais tempo para atender uma solicitação.

		def avgTimeTakenByKey(rdd):
		    return rdd.combineByKey(lambda line: (line.time_taken, 1),
		                            lambda x, line: (x[0] + line.time_taken, x[1] + 1),
		                            lambda x, y: (x[0] + y[0], x[1] + y[1]))\
		              .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
		    
		avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

	Você verá algo semelhante ao mostrado a seguir:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[(u'/blogposts/mvc4/step13.png', 197.5),
		 (u'/blogposts/mvc2/step10.jpg', 179.5),
		 (u'/blogposts/extractusercontrol/step5.png', 170.0),
		 (u'/blogposts/mvc4/step8.png', 159.0),
		 (u'/blogposts/mvcrouting/step22.jpg', 155.0),
		 (u'/blogposts/mvcrouting/step3.jpg', 152.0),
		 (u'/blogposts/linqsproc1/step16.jpg', 138.75),
		 (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
		 (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
		 (u'/blogposts/nested/step2.jpg', 126.0),
		 (u'/blogposts/adminpack/step1.png', 124.0),
		 (u'/BlogPosts/datalistpaging/step2.png', 118.0),
		 (u'/blogposts/mvc4/step35.png', 117.0),
		 (u'/blogposts/mvcrouting/step2.jpg', 116.5),
		 (u'/blogposts/aboutme/basketball.jpg', 109.0),
		 (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
		 (u'/blogposts/mvc4/step12.png', 106.0),
		 (u'/blogposts/linq8/step0.jpg', 105.5),
		 (u'/blogposts/mvc2/step18.jpg', 104.0),
		 (u'/blogposts/mvc2/step11.jpg', 104.0),
		 (u'/blogposts/mvcrouting/step1.jpg', 104.0),
		 (u'/blogposts/extractusercontrol/step1.png', 103.0),
		 (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
		 (u'/blogposts/mvcrouting/step21.jpg', 101.0),
		 (u'/blogposts/mvc4/step1.png', 98.0)]


13. Você também pode apresentar essas informações na forma de plotagem. Como uma primeira etapa para criar um gráfico, criaremos uma tabela **AverageTime** temporária. A tabela agrupa os logs por hora para ver se houve quaisquer picos incomuns de latência em um determinado momento.

		avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
		schema = StructType([StructField('Minutes', IntegerType(), True),
		                     StructField('Time', FloatType(), True)])
		                     
		avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
		avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Você pode então executar a seguinte consulta SQL para obter todos os registros da tabela **AverageTime**.

		%%sql -o averagetime
		SELECT * FROM AverageTime

	A mágica do `%%sql` seguido pelo `-o averagetime` assegura que a saída da consulta sejam mantida localmente no servidor Jupyter (normalmente o nó principal do cluster). A saída é mantida como uma estrutura de dados [Pandas](http://pandas.pydata.org/) com o nome especificado **averagetime**.

	Você verá algo semelhante ao mostrado a seguir:

	![Saída da consulta SQL](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Saída da consulta SQL")

	Para obter mais informações sobre a mágica do `%%sql`, bem como outras mágicas disponíveis com o kernel PySpark, consulte [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Agora você pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados para criar um gráfico. Como o gráfico deve ser criado da estrutura de dados **averagetime** mantida localmente, o trecho de código deve começar com a mágica `%%local`. Isso garante que o código seja executado localmente no servidor do Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
		plt.xlabel('Time (min)')
		plt.ylabel('Average time taken for request (ms)')

	Você verá algo semelhante ao mostrado a seguir:

	![Saída de Matplotlib](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Saída de Matplotlib")

16. Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.
	

## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: use o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0420_2016-->