<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitoramento" pageTitle="Monitoramento do HDInsight | Azure" metaKeywords="" description="Saiba como monitorar um cluster HDInsight e exibir o histórico do trabalho do Hadoop por meio do Portal de Gerenciamento do Azure." metaCanonical="" services="hdinsight" documentationCenter="" title="Como monitorar o HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />




# Como monitorar o HDInsight

Neste tópico, você aprenderá como monitorar um cluster HDInsight.

##Sumário

* [Como monitorar um serviço HDInsight](#monitorcluster)
* [Como exibir o histórico do trabalho do Hadoop](#jobhistory)

##<a id="monitorcluster"></a> Como monitorar um cluster HDInsight

Para monitorar a integridade de um cluster HDInsight e os trabalhos do Hadoop em execução no cluster, você pode conectar-se ao Painel do HDInsight e clicar no bloco Monitorar Cluster.

![HDI.TileMonitorCluster][hdi-monitor-cluster-tile]

A página Monitorar é semelhante a:

![HDI.MonitorPage][hdi-monitor-page]


À direita, são mostrados o Namenode e o controlador do trabalho que estão em execução e os quatro nós de dados que estão em execução no estado íntegro.

À esquerda, são mostradas as métricas da redução do mapa nos últimos 30 minutos. Você pode alterar as janelas do monitor para 30 minutos, 1 hora, 3 horas, 12 horas, 1 dia, 2 dias, 1 semana e 2 semanas.

##<a id="jobhistory"></a> Como exibir o histórico do trabalho do Hadoop
Para exibir o histórico do trabalho do Hadoop, conecte-se ao Painel do HDInsight e, em seguida, clique no bloco Histórico do Trabalho. 

![HDI.TileJobHistory][hdi-job-history-tile]

A imagem mostra o número de trabalhos que foram executados. Por exemplo, a imagem anterior indica que o histórico do trabalho está disponível para seis trabalhos.  A página de histórico do trabalho é semelhante à seguinte:

![HDI.JobHistoryPage][hdi-job-history-page]


## Consulte também

* [Como administrar o HDInsight](/pt-br/manage/services/hdinsight/howto-administer-hdinsight/)
* [Como implantar um cluster HDInsight de forma programática](/pt-br/manage/services/hdinsight/howto-deploy-cluster/)
* [Como executar trabalhos remotos no cluster do HDInsight de forma programática](/pt-br/manage/services/hdinsight/howto-execute-jobs-programmatically/)
* [Tutorial: Introdução ao HDInsight do Azure](/pt-br/manage/services/hdinsight/get-started-hdinsight/)

[hdi-monitor-cluster-tile]: ./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG
[hdi-monitor-page]: ./media/hdinsight-monitor/HDI.MonitorPage.PNG
[hdi-job-history-tile]: ./media/hdinsight-monitor/HDI.TileJobHistory.PNG
[hdi-job-history-page]: ./media/hdinsight-monitor/HDI.JobHistoryPage.PNG

