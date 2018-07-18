---
title: Log Analytics para Apache Kafka – Azure HDInsight | Microsoft Docs
description: Saiba como usar o Log Analytics para analisar logs do cluster Apache Kafka no Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9f366631ced4392831ad9ed97898a88b3290cd22
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise logs do Apache Kafka no HDInsight

Saiba como usar o Log Analytics para analisar logs gerados pelo Apache Kafka no HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Habilitar Log Analytics para Kafka

As etapas para habilitar o Log Analytics para o HDInsight são as mesmas para todos os clusters HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Criar um espaço de trabalho do Log Analytics. Para obter mais informações, consulte o documento [Introdução ao espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Criar um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

3. Configure o cluster Kafka para usar o Log Analytics. Para obter mais informações, consulte o documento [Usar o Log Analytics para monitorar o HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > Também é possível configurar o cluster para usar o Log Analytics por meio do o cmdlet `Enable-AzureRmHDInsightOperationsManagementSuite`. Esse cmdlet requer as seguintes informações:
    >
    > * O nome do cluster HDInsight.
    > * A ID do espaço de trabalho do Log Analytics. Você pode encontrar a ID do espaço de trabalho no seu espaço de trabalho do Log Analytics.
    > * A chave primária da conexão do Log Analytics. Para localizar a chave primária, selecione a instância do Log Analytics e, em seguida, __Portal do OMS__. No portal do OMS, selecione __Configurações__, __Fontes Conectadas__ e, em seguida, __Servidores Linux__.


> [!IMPORTANT]
> Pode levar cerca de 20 minutos antes dos dados serem disponibilizados para o Log Analytics.

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

Para obter mais informações sobre o Log Analytics, consulte o documento de [Introdução ao espaço de trabalho do Log Analytics](../../log-analytics/log-analytics-get-started.md).

Para saber mais sobre como trabalhar com o Kafka, confira os seguintes documentos:

 * [Espelhamento do Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
 * [Aumentar a escalabilidade do Kafka no HDInsight](apache-kafka-scalability.md)
 * [Usar o streaming do Spark (DStreams) com o Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Usar o streaming estruturado do Spark com Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
