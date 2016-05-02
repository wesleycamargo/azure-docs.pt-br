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
	ms.date="04/14/2016" 
	ms.author="nitinme"/>


# Gerenciar recursos do cluster Apache Spark no HDInsight Linux (Preview)

O Spark no Azure HDInsight (Linux) fornece a interface do usuário do Ambari Web para gerenciar os recursos de cluster e monitorar a integridade do cluster. Você também pode usar o Servidor de Histórico do Spark para controlar os aplicativos cuja execução foi finalizada no cluster. É possível usar a interface do usuário do YARN para monitorar aqueles que atualmente estão em execução no cluster. Este artigo fornece instruções sobre como acessar essas interfaces do usuário e como realizar algumas tarefas básicas de gerenciamento de recursos usando essas interfaces.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, confira [Create Apache Spark clusters in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Como inicio a interface do usuário do Ambari Web?

1. No [Portal do Azure](https://ms.portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**. 
 
2. Na folha do cluster Spark, clique em **Painel**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

	![Iniciar Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Iniciar Gerenciador de Recursos")

3. Isso deve iniciar a interface do usuário do Ambari Web, como mostrado abaixo.

	![Interface do usuário da Ambari Web](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interface do usuário da Ambari Web")

## Como inicio o Servidor de Histórico do Spark?

1. No [Portal do Azure](https://ms.portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial).

2. Na folha do cluster, em **Links Rápidos**, clique em **Painel do Cluster**. Na folha **Painel do Cluster**, clique em **Servidor de Histórico do Spark**.

	![Servidor de Histórico do Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Servidor de Histórico do Spark")

	Quando solicitado, insira as credenciais de administrador para o cluster Spark.


## Como inicio a interface do usuário do Yarn?

É possível usar a interface do usuário do YARN para monitorar aplicativos que estão em execução no momento no cluster Spark. Para acessar a interface do usuário do YARN, é preciso ter habilitado o túnel SSH para o cluster. Para obter instruções, confira [Usar túnel SSH para acessar a interface do usuário do Ambari Web](hdinsight-linux-ambari-ssh-tunnel.md)

1. Inicie a interface do usuário do Ambari Web, conforme mostrado na seção acima.

2. Na interface do usuário do Ambari Web, selecione YARN na lista à esquerda da página.

3. 3\.Quando as informações do serviço YARN forem exibidas, escolha **Links Rápidos**. Será exibida uma lista de nós de cabeçalho do cluster. Escolha um dos nós de cabeçalho e **Interface de Usuário do ResourceManager**.

	![Iniciar Interface do usuário do YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Iniciar Interface do usuário do YARN")

4. Isso deve iniciar a interface do usuário do YARN e você deverá ver uma página semelhante à seguinte:

	![Interface do usuário do YARN](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "Interface do usuário do YARN")

##<a name="scenariosrm"></a>Como gerencio recursos usando a interface do usuário do Ambari Web?

Eis aqui alguns cenários comuns que você pode executar com o cluster Spark e as instruções sobre como tratar os que usam a interface do usuário do Ambari Web.

### Não uso o BI com cluster Spark. Como recupero os recursos?

1. Inicie a interface do usuário do Ambari Web, conforme mostrado acima. No painel de navegação à esquerda, clique em **Spark** e em **Configurações**.

2. Na lista de configurações disponíveis, procure por **Spark-thrift-sparkconf personalizado** e altere os valores de **spark.executor.memory** e **spark.drivers.core** para **0**.

	![Recursos de BI](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Recursos de BI")

3. Clique em **Salvar**. Insira uma descrição das alterações realizadas e clique em **Salvar** novamente.

4. Na parte superior da página, você verá um aviso para reiniciar o serviço Spark. Clique em **Reiniciar** para que as alterações entrem em vigor.


### Meus blocos de anotações do Jupyter não estão sendo executados conforme esperado. Como é possível reiniciar o serviço?

1. Inicie a interface do usuário do Ambari Web, conforme mostrado acima. No painel de navegação esquerdo, clique em **Jupyter**, em **Ações de Serviço** e em **Reiniciar Tudo**. Isso iniciará o serviço Jupyter em todos os nós de cabeçalho.

	![Reiniciar Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Reiniciar Jupyter")

	


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

* [Kernels disponíveis para o bloco de anotações do Jupyter no cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0420_2016-->