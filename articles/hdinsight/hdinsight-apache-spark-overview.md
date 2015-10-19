<properties 
	pageTitle="Uma visão geral do Apache Spark no HDInsight | Microsoft Azure" 
	description="Uma introdução ao Apache Spark no HDInsight e cenários nos quais usar o Spark no HDInsight em seus aplicativos." 
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
	ms.date="09/30/2015" 
	ms.author="nitinme"/>

# Visão geral: Apache Spark no Azure HDInsight 
 
O <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> é uma estrutura de processamento paralelo de código-fonte aberto que dá suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina. O Spark também é compatível com o armazenamento de blob do Azure (WASB) para possibilitar o fácil processamento dos dados existentes armazenados no Azure por meio do Spark.

Quando você provisiona um cluster do Spark no HDInsight, provisiona recursos de computação do Azure com o Spark instalado e configurado. Demora apenas cerca de dez minutos para provisionar um cluster do Spark no HDInsight. Os dados a serem processados são colocados no armazenamento de blob do Azure. Consulte [Usar o armazenamento de blob do Azure com HDInsight][hdinsight-storage].

![Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-overview/SparkArchitecture.png "Apache Spark no Azure HDInsight")


**Deseja começar com o Apache Spark no Azure HDInsight?** Consulte [Início rápido: provisionar um cluster do Spark no HDInsight e executar aplicativos de exemplo usando Jupyter e Zeppelin](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).



Assista a um vídeo de visão geral que descreve o Apache Spark no Azure HDInsight.

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## Por que usar o Spark no Azure HDInsight? 

O Azure HDInsight oferece um serviço Spark totalmente gerenciado. Os benefícios de usar o Spark no HDInsight são:

| Recurso | Descrição |
|-------------------------------------|-------------------|
| Facilidade de provisionamento | Você pode provisionar um novo cluster do Spark no HDInsight em minutos usando o Portal de Gerenciamento do Azure, o Azure PowerShell ou o SDK do HDInsight .NET. Consulte [Provisionar um cluster do Spark no HDInsight](hdinsight-apache-spark-provision-clusters.md) |
| Fácil de uso | Os clusters do Spark no HDInsight incluem blocos de anotações do Zeppelin e Jupyter pré-configurados. Você pode usá-los para processar e visualizar dados interativamente. As URLs desses blocos de anotação são https://CLUSTERNAME.azurehdinsight.net/zeppelin e https://CLUSTERNAME.azurehdinsight.net/jupyter. Substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight.|
| APIs REST | O Spark no HDInsight inclui um servidor de trabalho do Spark, um servidor de API REST que permite aos usuários enviar e monitorar trabalhos em execução remotamente. |
| Consultas simultâneas | O Spark no HDInsight dá suporte a consultas simultâneas. Isso possibilita que várias consultas de um usuário ou várias consultas de vários usuários e aplicativos compartilhem os mesmos recursos de cluster. |
| Armazenamento em cache no SSDs | Você pode escolher os dados em cache na memória ou em SSDs anexados a nós do cluster. O armazenamento em cache na memória oferece o melhor desempenho de consulta, mas pode ser caro; o armazenamento em cache em SSDs é uma ótima opção para melhorar o desempenho de consulta sem necessidade de criar um cluster do tamanho necessário para colocar todo o conjunto de dados na memória.|
| Integração com serviços do Azure | O Spark no HDInsight é fornecido com um conector para hubs de eventos do Azure. Os clientes podem criar aplicativos de fluxo contínuo usando os hubs de eventos, além do [Kafka](http://kafka.apache.org/), que já está disponível como parte do Spark. |
| Integração com ferramentas de BI | O Spark para HDInsight fornece conectores para ferramentas de BI populares, como [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop), para análise de dados.|
| Bibliotecas Anaconda pré-carregadas | Os clusters do Spark no HDInsight são fornecidos com bibliotecas Anaconda pré-instaladas. O [Anaconda](http://docs.continuum.io/anaconda/) fornece quase 200 bibliotecas de aprendizado de máquina, análise de dados, visualização, etc.|
| Escalabilidade | Embora você possa especificar o número de nós no cluster durante a criação, convém aumentar ou reduzir o cluster de acordo com a carga de trabalho. Todos os clusters do HDInsight permitem alterar o número de nós no cluster. Além disso, os clusters do Spark podem ser descartados sem perda de dados, pois todos os dados ficam no armazenamento de blob do Azure. |
| Suporte contínuo | O Spark no HDInsight é fornecido com suporte contínuo de nível empresarial e um SLA de 99,9% de tempo de atividade.|



## Quais são os casos de uso do Spark no HDInsight?

O Apache Spark no HDInsight permite os seguintes cenários principais.

### Análise de dados interativa e BI

[Examinar um tutorial](hdinsight-apache-spark-use-bi-tools.md)

O Apache Spark no HDInsight armazena dados em blobs do Azure. Especialistas de negócios e os principais responsáveis por tomar decisões podem analisar e criar relatórios com esses dados e usar o Microsoft Power BI para criar relatórios interativos por meio dos dados analisados. Os analistas podem começar com dados não estruturados/semiestruturados no armazenamento do Azure, definir um esquema para os dados usando blocos de anotações e, em seguida, criar modelos de dados usando o Microsoft Power BI. O Spark no HDInsight também dá suporte a várias ferramentas de BI de terceiros, como Tableau, Qlikview e SAP Lumira, tornando-o uma plataforma ideal para analistas de dados, especialistas de negócios e os principais responsáveis por tomar decisões.

### Aprendizado de máquina iterativo

[Examinar um tutorial](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

O Apache Spark vem com [MLlib](http://spark.apache.org/mllib/), uma biblioteca aprendizado de máquina criada com base no Spark. Além disso, o Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com uma variedade de pacotes para aprendizado de máquina. Junte isso a um suporte interno para blocos de anotações do Jupyter e terá um ambiente de alto nível para criar aplicativos de aprendizado de máquina.

### Análise de dados de streaming e em tempo real

[Examinar um tutorial](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

A análise de dados em tempo real é usada para cenários que vão de reduzir o tempo para obter insight de dados processando os dados conforme eles chegam até criar uma verdadeira solução de fluxo. O Spark no HDInsight oferece suporte avançado para criar soluções de análise em tempo real. Embora o Spark já tenha conectores para receber dados de várias fontes, como soquetes TCP, Flume, Twitter, ZeroMQ ou Kafka, o Spark no HDInsight adiciona suporte de primeira classe para a inserção de dados de hubs de evento do Azure. Os Hubs de Eventos são o serviço de enfileiramento de mensagens mais usado no Azure. Ter um excelente suporte pronto para uso para hubs de eventos torna o Spark no HDInsight a plataforma ideal para a criação de pipeline de análise em tempo real.

##<a name="next-steps"></a>Quais componentes estão incluídos como parte de um cluster do Spark?

O Spark no HDInsight inclui os seguintes componentes que estão disponíveis nos clusters por padrão.

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/). Vem com Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Spark Job Server](https://github.com/spark-jobserver/spark-jobserver)
- [Bloco de anotações do Zeppelin](https://zeppelin.incubator.apache.org)
- [Bloco de anotações do Jupyter](https://jupyter.org)

O Spark no HDInsight também fornece um [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para conectividade aos clusters do Spark no HDInsight por meio de ferramentas de BI, como Microsoft Power BI e Tableau.

##<a name="see-also"></a>Consulte também

* [Início rápido: use o Spark no HDInsight com o Bloco de anotações do Zeppelin para executar a análise de dados interativa](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Provisionar um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Enviar trabalhos remotamente para um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=Oct15_HO2-->