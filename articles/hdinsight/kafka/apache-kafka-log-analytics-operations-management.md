---
title: Logs de Monitor do Azure para Apache Kafka – Azure HDInsight
description: Saiba como usar os logs do Azure Monitor para analisar logs do cluster do Apache Kafka no HDInsight do Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: b4eced461f798f3cbf3ce968dae59cfb8f1a0363
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115087"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise logs do Apache Kafka no HDInsight

Saiba como usar os logs do Azure Monitor para analisar logs gerados pelo Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Habilitar os logs do Azure Monitor para o Apache Kafka

As etapas para habilitar os logs do Azure Monitor para HDInsight são as mesmas para todos os clusters do HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Criar um espaço de trabalho do Log Analytics. Para obter mais informações, consulte o documento [Introdução ao espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Criar um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

3. Configure o cluster Kafka para usar os logs do Azure Monitor. Para obter mais informações, consulte o [logs de uso do Azure Monitor para monitorar o HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) documento.

    > [!NOTE]  
    > Você também pode configurar o cluster para usar os logs do Azure Monitor, usando o `Enable-AzHDInsightOperationsManagementSuite` cmdlet. Esse cmdlet requer as seguintes informações:
    >
    > * O nome do cluster HDInsight.
    > * A ID do espaço de trabalho para os logs do Azure Monitor. Você pode encontrar a ID do espaço de trabalho no seu espaço de trabalho do Log Analytics.
    > * A chave primária para a conexão de análise de log. Para localizar a chave primária, abra o workspace no portal do Azure, selecione __Configurações Avançadas__ no menu à esquerda. Em configurações Avançadas, selecione __Fontes Conectadas__>__Servidores Linux__.


> [!IMPORTANT]  
> Ele pode levar cerca de 20 minutos para que dados estejam disponíveis para os logs do Azure Monitor.

## <a name="query-logs"></a>Logs de consulta

1. No [Portal do Azure](https://portal.azure.com), selecione o espaço de trabalho do Log Analytics.

2. Selecione __Pesquisa de Logs__. A partir desse ponto, é possível pesquisar os dados coletados do Kafka. Veja a seguir alguns exemplos de pesquisas:

   * Uso do disco: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Uso da CPU: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Mensagens de entrada por segundo: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * Bytes de entrada por segundo: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * Bytes de saída por segundo: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > Substitua os valores de consulta com as informações específicas do cluster. Por exemplo, `ClusterName_s` deve ser definido como o nome do cluster. `HostName_s` deve ser definido como o nome de domínio de um nó de trabalho no cluster.

     Também é possível inserir `*` para pesquisas todos os tipos registrados. Atualmente, os logs a seguir estão disponíveis para consultas:

     | Tipo de log | DESCRIÇÃO |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | log\_kafkacontroller\_CL | Kafka broker controller.log |
     | metrics\_kafka\_CL | Métricas do Kafka JMX |

     ![Imagem da pesquisa de uso da CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Monitor, consulte a [visão geral do Azure Monitor](../../log-analytics/log-analytics-get-started.md) documento.

Para saber mais sobre como trabalhar com o Apache Kafka, confira os seguintes documentos:

 * [Espelhamento do Apache Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
 * [Aumentar a escalabilidade do Apache Kafka no HDInsight](apache-kafka-scalability.md)
 * [Use o fluxo do Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Use o fluxo estruturado do Apache Spark com o Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
