---
title: Rastrear e depurar trabalhos em execução no cluster do Apache Spark no HDInsight | Microsoft Docs
description: Use a interface do usuário do YARN, a interface do usuário do Spark e o Servidor de Histórico do Spark para rastrear e depurar trabalhos em execução no cluster Spark no Azure HDInsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: nitinme

---
# Rastrear e depurar trabalhos em execução no cluster do Apache Spark no HDInsight Linux
Neste artigo, você aprenderá como rastrear e depurar trabalhos do Spark usando a interface do usuário do YARN, a interface do usuário do Spark e o Servidor de histórico do Spark. Para este artigo, começaremos um trabalho do Spark usando um notebook disponível com o cluster Spark, **Aprendizado de máquina: análise preditiva nos dados de inspeção de alimentos usando MLLib**. Você pode usar as etapas a seguir para rastrear um aplicativo que foi enviado usando qualquer outra abordagem, por exemplo, **spark-submit**.

## Pré-requisitos
Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Você deve ter começado a executar o notebook **[Aprendizado de máquina: análise preditiva nos dados de inspeção de alimentos usando MLLib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este notebook, siga o link.

## Rastrear um aplicativo na interface do usuário do YARN
1. Inicie a interface do usuário do YARN. Na folha do cluster, clique em **Painel do Cluster** e em **YARN**.
   
    ![Iniciar Interface do usuário do YARN](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Alternativamente, também é possível iniciar a interface do usuário do YARN na interface do usuário do Ambari. Para iniciar a interface de usuário do Ambari, na folha do cluster, clique em **Painel do Cluster** e em **Painel do Cluster HDInsight**. Na interface do usuário do Ambari, clique em **YARN**, em **Links Rápidos**, no gerenciador de recursos ativo e, por fim, em **Interface do Usuário do Resource Manager**.
   > 
   > 
2. Como você iniciou o trabalho do Spark usando notebooks Jupyter, o aplicativo tem o nome **remotesparkmagics** (esse é o nome de todos os aplicativos que são iniciados do notebook). Clique na ID de aplicativo com o nome do aplicativo para obter mais informações sobre o trabalho. Isso inicia o modo de exibição do aplicativo.
   
    ![Localizar a ID de aplicativo Spark](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)
   
    Para aplicativos que são iniciados do notebook Jupyter, o status é sempre **EM EXECUÇÃO** até que você saia do notebook.
3. Na exibição de aplicativo, você pode fazer drill down para descobrir os contêineres associados ao aplicativo e os logs (stdout/stderr). Você também pode iniciar a interface do usuário do Spark clicando no link correspondente para a **URL de Rastreamento**, conforme mostrado abaixo.
   
    ![Baixar logs de contêiner](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## Rastrear um aplicativo na interface do usuário do Spark
Na interface do usuário do Spark, é possível fazer drill down em trabalhos do Spark que são gerados pelo aplicativo iniciado anteriormente.

1. Para iniciar a interface do usuário do Spark, da exibição do aplicativo, clique no link em **URL de Rastreamento**, conforme mostrado na captura de tela acima. Você pode ver todos os trabalhos do Spark que são iniciados pelo aplicativo em execução no notebook do Jupyter.
   
    ![Exibir trabalhos do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)
2. Clique na guia **Executores** para ver informações de processamento e armazenamento de cada executor. Você também pode recuperar a pilha de chamadas clicando no link **Thread Dump** (Despejo de Thread).
   
    ![Exibir executores do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
3. Clique na guia **Estágios** para ver os estágios associados ao aplicativo.
   
    ![Exibir estágios do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)
   
    Cada estágio pode ter várias tarefas para as quais você pode exibir estatísticas de execução, como mostrado abaixo.
   
    ![Exibir estágios do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png)
4. Na página de detalhes do estágio, você pode iniciar Visualização de DAG. Expanda o link **DAG Visualization** (Visualização de DAG) na parte superior da página, como mostrado abaixo.
   
    ![Exibir a visualização de DAG dos estágios do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    O DAG ou Gráfico Acíclico Direto representa os diferentes estágios no aplicativo. Cada caixa azul no gráfico representa uma operação do Spark iniciada do aplicativo.
5. Na página de detalhes do estágio, você também pode iniciar o modo de exibição de linha do tempo do aplicativo. Expanda o link **Event Timeline** (Linha do Tempo do Evento) na parte superior da página, como mostrado abaixo.
   
    ![Exibir linha do tempo de evento de estágios do Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Isso exibe os eventos do Spark na forma de uma linha do tempo. O modo de exibição de tempo está disponível em três níveis, entre trabalhos, dentro de um trabalho e dentro de um estágio. A imagem acima captura o modo de exibição de linha do tempo de um determinado estágio.
   
   > [!TIP]
   > Se você selecionar a caixa de seleção **Enable zooming** (Habilitar zoom), poderá rolar para a esquerda e para a direita no modo de exibição de linha do tempo.
   > 
   > 
6. Outras guias na interface do usuário do Spark fornecem informações úteis sobre a instância do Spark.
   
   * Guia Armazenamento: se seu aplicativo criar RDDs, você encontrará informações sobre isso na guia Armazenamento.
   * Guia Ambiente: esta guia fornece muitas informações úteis sobre sua instância do Spark, como a
     * Versão da escala
     * Diretório de log de eventos associado ao cluster
     * Número de núcleos de executor do aplicativo
     * Etc.

## Encontrar informações sobre trabalhos concluídos usando o Servidor de Histórico do Spark
Quando um trabalho é concluído, as informações sobre ele são mantidas no Servidor de Histórico do Spark.

1. Para iniciar o Servidor de Histórico do Spark, na folha do cluster, clique em **Painel do Cluster** e, em seguida, clique em **Servidor de Histórico do Spark**.
   
    ![Iniciar o Servidor de Histórico do Spark](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Alternativamente, também é possível iniciar a interface do usuário do Servidor de Histórico do Spark na interface do usuário do Ambari. Para iniciar a interface do usuário do Ambari, na folha do cluster, clique em **Painel do Cluster** e em **Painel do Cluster HDInsight**. Na interface do usuário do Ambari, clique em **Spark**, clique em **Links Rápidos**, e, em seguida, clique em **Spark History Server UI** (Interface do usuário do Servidor de Histórico do Spark).
   > 
   > 
2. Você verá todos os aplicativos concluídos listados. Clique em uma ID de aplicativo para fazer o drill down em um aplicativo para obter mais informações.
   
    ![Iniciar o Servidor de Histórico do Spark](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)

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
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0914_2016-->