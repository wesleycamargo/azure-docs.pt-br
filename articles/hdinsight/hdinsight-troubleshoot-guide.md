---
title: "Documentação do HDInsight do Azure – guia de solução de problemas | Microsoft Docs"
description: "Solucione problemas de cargas de trabalho do Hadoop no HDInsight. A documentação passo a passo mostra como resolver problemas comuns com Hive, Spark, HBase, Storm, Kafka no HDInsight."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 9313f0713818f80bae99c32a438d97ba544a7a6b
ms.contentlocale: pt-br
ms.lasthandoff: 08/17/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Solução de problemas do Azure HDInsight

| Carga de trabalho do Apache | Principais perguntas |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Solucionar problemas do HBASE](hdinsight-troubleshoot-hbase.md)|<br>[Como fazer para executar relatórios de comando hbck com várias regiões não atribuídas](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Como fazer para corrigir problemas de tempo limite com comandos hbck atribuições de região](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Como fazer para forçar a desabilitação do modo de segurança de HDFS em um cluster](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[Como fazer para corrigir problemas de conectividade de JDBC ou sqlline com o Apache Phoenix](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[O que faz com o servidor mestre falhar ao iniciar](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[O que causa uma falha de reinicialização em um servidor de região](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Solucionar problemas do HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Como fazer para acessar o HDFS local de dentro de um cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Como fazer para forçar a desabilitação do modo de segurança de HDFS em um cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Solucionar problemas do HIVE](hdinsight-troubleshoot-hive.md)|<br>[Como fazer para exportar um metastore do Hive e importá-lo para outro cluster](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Como localizar os logs do Hive em um cluster](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[Como fazer para inicializar o shell do Hive com configurações específicas em um cluster](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Como fazer para analisar dados de DAG do Tez em um caminho crítico do cluster](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Como fazer para baixar dados de DAG do Tez de um cluster](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Solucionar problemas do Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Como fazer para configurar um aplicativo Spark por meio de Ambari nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark por meio de um bloco de anotações do Jupyter nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark por meio de LIVY nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark por meio de spark-submit clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[O que causa uma exceção OutOfMemoryError de um aplicativo Spark](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Solucionar problemas do STORM](hdinsight-troubleshoot-STORM.md)|<br>[Como fazer para acessar a interface do usuário do Storm em um cluster](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[Como fazer para transferir informações do ponto de verificação do spout Storm eventhub de uma topologia para outra](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Como fazer para localizar binários do Storm em um cluster](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Como fazer para determinar a topologia de implantação de um cluster do Storm](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Como fazer para localizar binários Storm-EventHub-Spout para desenvolvimento](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[Como fazer para localizar arquivos de configuração do Storm Log4J nos clusters](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Solucionar problemas do YARN](hdinsight-troubleshoot-YARN.md)|<br>[Como fazer para criar uma nova fila do Yarn em um cluster](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Como fazer para baixar logs do Yarn de um cluster](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Recursos de solução de problemas do HDInsight
Consulte os seguintes artigos para obter ajuda adicional sobre solução de problemas.

| Para obter informações sobre | Consulte o seguinte |
| --- | --- |
| HDInsight no Linux e otimização | [Informações sobre o uso do HDInsight no Linux](hdinsight-hadoop-linux-information.md)<br>[Desempenho e memória do Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Desempenho de consulta de Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Logs e despejos de memória | [Acesso aos logs de aplicativo Hadoop YARN no Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[Habilitar os despejos de pilha para os serviços Hadoop](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| Erros | [Entenda e resolva erros do WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[Configurações do Hive corrigem erro de Memória Insuficiente](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Ferramentas | [Usar Exibições do Ambari para depurar Trabalhos do Tez](hdinsight-debug-ambari-tez-view.md)<br>[Otimizar consultas do Hive](hdinsight-hadoop-optimize-hive-query.md) |



