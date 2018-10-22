---
title: Log Analytics para Apache Kafka – Azure HDInsight
description: Saiba como usar o Log Analytics para analisar logs do cluster Apache Kafka no Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: d07c37803c94d7fa39d398a97796fecbad373af4
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047819"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise logs do Apache Kafka no HDInsight

Saiba como usar o Log Analytics para analisar logs gerados pelo Apache Kafka no HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Habilitar Log Analytics para Kafka

As etapas para habilitar o Log Analytics para o HDInsight são as mesmas para todos os clusters HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Criar um workspace do Log Analytics. Para obter mais informações, consulte o documento [Introdução ao workspace do Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Criar um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

3. Configure o cluster Kafka para usar o Log Analytics. Para obter mais informações, consulte o documento [Usar o Log Analytics para monitorar o HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > Também é possível configurar o cluster para usar o Log Analytics por meio do o cmdlet `Enable-AzureRmHDInsightOperationsManagementSuite`. Esse cmdlet requer as seguintes informações:
    >
    > * O nome do cluster HDInsight.
    > * A ID do workspace do Log Analytics. Você pode encontrar a ID do workspace no seu workspace do Log Analytics.
    > * A chave primária da conexão do Log Analytics. Para localizar a chave primária, abra o workspace no portal do Azure, selecione __Configurações Avançadas__ no menu à esquerda. Em configurações Avançadas, selecione __Fontes Conectadas__>__Servidores Linux__.


> [!IMPORTANT]
> Pode levar cerca de 20 minutos antes dos dados serem disponibilizados para o Log Analytics.

## <a name="query-logs"></a>Logs de consulta

1. No [Portal do Azure](https://portal.azure.com), selecione o workspace do Log Analytics.

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

Para obter mais informações sobre o Log Analytics, consulte o documento de [Introdução ao workspace do Log Analytics](../../log-analytics/log-analytics-get-started.md).

Para saber mais sobre como trabalhar com o Kafka, confira os seguintes documentos:

 * [Espelhamento do Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
 * [Aumentar a escalabilidade do Kafka no HDInsight](apache-kafka-scalability.md)
 * [Usar o streaming do Spark (DStreams) com o Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Usar o streaming estruturado do Spark com Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
