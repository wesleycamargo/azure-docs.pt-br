<properties 
	pageTitle="Use o Gerenciador de Recursos para alocar recursos para o cluster do Apache Spark no HDInsight | Microsoft Azure" 
	description="Saiba como usar o Gerenciador de Recursos para clusters do Spark no HDInsight para melhorar o desempenho." 
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


# Gerenciar recursos do cluster Apache Spark no HDInsight Windows (Preview)

> [AZURE.NOTE] O HDInsight agora fornece clusters Spark no Linux. Para obter informações sobre como gerenciar recursos de um cluster do Spark no HDInsight no Linux, confira [Gerenciar recursos de cluster do Apache Spark no Azure HDInsight (Linux)](hdinsight-apache-spark-resource-manager.md).

O gerenciador de recursos é um componente do painel de cluster do Spark que permite que você gerencie recursos, como cores e RAM usada por cada aplicativo em execução no cluster.

## <a name="launchrm"></a>Como iniciar o Gerenciador de Recursos?

1. No [Portal do Azure](https://ms.portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**. 
 
2. Na folha do cluster Spark, clique em **Painel**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

	![Inicializar Gerenciador de Recursos](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "Iniciar Gerenciador de Recursos")

##<a name="scenariosrm"></a>Como posso corrigir estes problemas usando o Gerenciador de Recursos?

Aqui estão alguns cenários comuns que você pode executar com o cluster do Spark e as instruções sobre como tratar os que usam o Gerenciador de Recursos.

### Meu cluster do Spark no HDInsight está lento

O cluster do Apache Spark no HDInsight foi projetado para multilocação, para que os recursos fossem divididos em vários componentes (blocos de anotações, servidor de trabalho, etc.). Isso permite que você use todos os componentes do Spark simultaneamente sem se preocupar sobre qualquer componente que não consegue fazer os recursos executarem, mas cada componente poderá ser mais lento, já que os recursos são fragmentados. Isso pode ser ajustado com base em suas necessidades.


### Apenas uso o bloco de anotações do Jupyter com o cluster do Spark. Como posso alocar todos os recursos a ele?

1. No **Painel do Spark**, clique na guia **IU do Spark** para descobrir o número máximo de núcleos e o máximo de RAM que você pode alocar para os aplicativos.

	![Alocação de recurso](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Encontre os recursos alocados para um cluster do Spark")

	Seguindo a captura de tela acima, o máximo de núcleos que você pode alocar é de 7 (total de 8 núcleos, dos quais 1 está em uso) e o máximo de RAM que você pode alocar é 9 GB (total de 12 GB de RAM, dos quais 2 GB devem ser definidos reservados para uso do sistema e 1 GB que está em uso por outros aplicativos).

	Você também deve incluir todos os aplicativos que estão executando. Você pode examinar os aplicativos em execução na guia **IU do Spark**.

	![Aplicativos em execução](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "Aplicativos em execução no cluster")

	
2. No painel Spark HDInsight, clique na guia **Gerenciador de Recursos** e especifique os valores para **Contagem de núcleos do aplicativo padrão** e **Memória do executor padrão por nó de trabalhador**. Defina outras propriedades como 0.

	![Alocação de recurso](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "Alocar recursos para seus aplicativos")

### Não uso ferramentas de BI com cluster do Spark. Como posso retornar os recursos? 

Especifique a contagem do núcleo de servidor de Thrift e memória de executor do servidor Thrift como 0. Sem núcleo ou memória alocados, o servidor Thrift entrará em um estado **EM ESPERA**.

![Alocação de recurso](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "Não há recursos no servidor Thrift")

##<a name="seealso"></a>Consulte também

* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Criar um Spark no cluster do HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0420_2016-->