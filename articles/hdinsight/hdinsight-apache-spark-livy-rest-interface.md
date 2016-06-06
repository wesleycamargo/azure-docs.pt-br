<properties
	pageTitle="Enviar trabalhos Spark remotamente usando a Livy | Microsoft Azure"
	description="Saiba como usar a Livy com clusters HDInsight para enviar trabalhos Spark remotamente."
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
	ms.date="05/24/2016"
	ms.author="nitinme"/>


# Enviar trabalhos do Spark remotamente para um cluster HDInsight Spark no Linux usando o Livy (Preview)

O cluster do Apache Spark no Azure HDInsight inclui Livy, uma interface REST para o envio de trabalhos remotamente para um cluster Spark. Para obter a documentação detalhada, confira [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Você pode usar a Livy para executar shells interativos do Spark ou enviar trabalhos em lotes a serem executados no Spark. Este artigo aborda como usar a Livy para enviar trabalhos em lotes. A sintaxe abaixo usa Curl para fazer chamadas REST para o ponto de extremidade da Livy.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, confira [Create Apache Spark clusters in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Enviar um trabalho em lotes ao cluster

Antes de enviar um trabalho em lotes, você deve carregar o jar do aplicativo no armazenamento de cluster associado ao cluster. Você pode usar [**AzCopy**](../storage/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível encontrar mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

	curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemplos**:

* Se o arquivo jar estiver no armazenamento de cluster (WASB)

		curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se quiser passar o nome do arquivo jar e o nome da classe como parte de um arquivo de entrada (nesse exemplo, input.txt)

		curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## Obter informações sobre lotes em execução no cluster

	curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se quiser recuperar todos os lotes em execução no cluster:

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se quiser recuperar um lote específico com uma batchID fornecida

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## Excluir um trabalho em lotes

	curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## Livy e alta disponibilidade

O Livy fornece alta disponibilidade para trabalhos Spark em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço Livy falhar depois de você enviar um trabalho remotamente a um cluster Spark, o trabalho continuará em execução em segundo plano. Quando o Livy for backup, ele restaurará o status do trabalho e enviará o relatório de volta.

* Os blocos de notas Jupyter para HDInsight são ativados pelo Livy no back-end. Se um bloco de anotações está executando um trabalho de Spark e o serviço Livy é reiniciado, o bloco de notas continuará a executar as células de código.

## Mostrar um exemplo

Nesta seção, vamos examinar exemplos sobre como usar a Livy para enviar um aplicativo Spark, monitorar o progresso do aplicativo e excluir o trabalho. O aplicativo que usamos neste exemplo é o desenvolvido no artigo [Criar um aplicativo Scala autônomo para ser executado no cluster Spark no HDInsight](hdinsight-apache-spark-create-standalone-application.md). As etapas a seguir pressupõem o seguinte:

* Você já copiou o jar do aplicativo para a conta de armazenamento associada ao cluster.
* Você tem o CuRL instalado no computador, onde está executando essas etapas.

Execute as seguintes etapas:

1. Primeiramente, vamos verificar se a Livy está em execução no cluster. Podemos fazer isso obtendo uma lista de lotes em execução. Se essa for a primeira vez que você está executando um trabalho usando a Livy, o resultado deverá ser zero.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

	Você deverá ver um resultado semelhante ao seguinte:

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:47:53 GMT
		< Content-Length: 34
		<
		{"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	Observe que a última linha da saída informa **total:0**, o que sugere que não há lotes em execução.

2. Agora vamos enviar um trabalho em lotes. O trecho abaixo usa um arquivo de entrada (input.txt) para passar o nome do jar e o nome da classe como parâmetros. Essa será a abordagem recomendada se você estiver executando essas etapas em um computador Windows.

		curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

	Os parâmetros no arquivo **input.txt** são definidos da seguinte maneira:

		{ "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

	Você deverá ver um resultado semelhante ao seguinte:

		< HTTP/1.1 201 Created
		< Content-Type: application/json; charset=UTF-8
		< Location: /0
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:51:30 GMT
		< Content-Length: 36
		<
		{"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	Observe que a última linha da saída informa **state:starting**. Assim como, **id:0**. Essa é a ID do lote.

3. Agora você pode recuperar o status desse lote específico usando a ID do lote.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	Você deverá ver um resultado semelhante ao seguinte:

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:54:42 GMT
		< Content-Length: 509
		<
		{"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	Agora a saída mostra **state:success**, o que sugere que o trabalho foi concluído com êxito.

4. Se quiser, você pode excluir o lote.

		curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	Você deverá ver um resultado semelhante ao seguinte:

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Sat, 21 Nov 2015 18:51:54 GMT
		< Content-Length: 17
		<
		{"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	A última linha da saída mostra que o lote foi excluído com êxito. Se você excluir um trabalho enquanto ele estiver em execução, isso praticamente eliminará o trabalho. Se você excluir um trabalho que foi concluído, com êxito ou não, essa ação excluirá por completo as informações sobre o trabalho.

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

### Ferramentas e extensões

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0525_2016-->