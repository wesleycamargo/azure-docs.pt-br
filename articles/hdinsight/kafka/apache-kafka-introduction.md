---
title: Uma introdução ao Apache Kafka no HDInsight - Azure | Microsoft Docs
description: 'Saiba mais sobre o Apache Kafka no HDInsight: o que é o que ele faz e onde encontrar exemplos e informações de introdução.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/11/2018
ms.author: larryfr
ms.openlocfilehash: 51b4e4dea0f0c4da739f9e40beb74931060dd22b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33770864"
---
# <a name="what-is-apache-kafka-on-hdinsight"></a>O que é Apache Kafka no HDInsight?

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de streaming distribuída de software livre que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real. O Kafka também fornece funcionalidade de agente de mensagem semelhante a uma fila de mensagens, onde você pode publicar e assinar os fluxos de dados nomeados. 

Estas são as características específicas do Kafka no HDInsight:

* Ele é um serviço gerenciado que fornece um processo de configuração simplificado. O resultado é uma configuração testada e com suporte da Microsoft.

* A Microsoft fornece um SLA (Contrato de Nível de Serviço) de 99,9% de tempo de atividade no Kafka. Para saber mais, veja o documento [Informações de SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Ele usa o Azure Managed Disks como repositório de backup para Kafka. O Managed Disks pode fornecer até 16 TB de armazenamento por agente do Kafka. Para saber mais sobre como configurar discos gerenciados com o Kafka no HDInsight, veja [Aumentar a escalabilidade do Kafka no HDInsight](apache-kafka-scalability.md).

    Para saber mais sobre discos gerenciados, veja [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* O Kafka foi criado com uma exibição única e bidimensional de um rack. O Azure separa um rack em duas dimensões: UD (domínios de atualização) e FD (domínios de falha). A Microsoft fornece ferramentas que redistribuem partições e réplicas do Kafka entre UDs e FDs. 

    Para obter mais informações, consulte [Alta disponibilidade com Kafka no HDInsight](apache-kafka-high-availability.md).

* O HDInsight permite que você altere o número de nós de trabalho (que hospedam o agente do Kafka) após a criação do cluster. O dimensionamento pode ser executado a partir do portal do Azure, do Azure PowerShell e de outras interfaces de gerenciamento do Azure. Para Kafka você deve redistribuir as réplicas de partição após as operações de dimensionamento. Redistribuir as partições permite ao Kafka aproveitar o novo número de nós de trabalho.

    Para obter mais informações, consulte [Alta disponibilidade com Kafka no HDInsight](apache-kafka-high-availability.md).

* O Azure Log Analytics pode ser usado para monitorar o Kafka no HDInsight. O Log Analytics expõe as informações do nível da máquina virtual, como o disco e as métricas NIC e as métricas JMX do Kafka.

    Para obter mais informações, consulte [Analisar logs do Kafka no HDInsight](apache-kafka-log-analytics-operations-management.md).

### <a name="kafka-on-hdinsight-architecture"></a>Arquitetura do Kafka no HDInsight

O diagrama a seguir mostra uma configuração típica de Kafka que usa grupos de consumidores, particionamento e a replicação oferece leitura paralela de eventos com tolerância a falhas:

![Diagrama de configuração do cluster Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

O Apache ZooKeeper gerencia o estado do cluster Kafka. O Zookeeper foi criado para executar transações simultâneas, resilientes e de baixa latência. 

O Kafka armazena registros (dados) em **tópicos**. Os registros são produzidos por **produtores** e consumidos por **consumidores**. Os produtores enviam registros aos **agentes** do Kafka. Cada nó de trabalho no cluster HDInsight é um agente do Kafka. 

Registros de partição de tópicos entre agentes. Ao consumir registros, você pode usar um consumidor por partição a fim de alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar partições entre os nós, protegendo contra falhas de nó (agente). Uma partição marcada com um *(L)* no diagrama é o líder da partição específica. O produtor tráfego é roteado para o preenchimento de cada nó, usando o estado gerenciado pelo ZooKeeper.

## <a name="why-use-kafka-on-hdinsight"></a>Por que usar o Kafka no HDInsight?

Veja a seguir tarefas e padrões comuns que podem ser executadas usando Kafka no HDInsight:

* **Replicação de dados Kafka**: O Kafka fornece o utilitário MirrorMaker, que replica os dados entre os clusters do Kafka.

    Para obter informações sobre como usar o MirrorMaker, consulte [Replicar tópicos Kafka com Kafka no HDInsight](apache-kafka-mirroring.md).

* **Padrão de mensagens publicar-assinar**: O Kafka fornece uma API de Produtor para publicação de registros em um tópico do Kafka. A API de Consumidor é usada na assinatura de um tópico.

    Para obter mais informações, consulte [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md).

* **Processamento de fluxo**: O Kafka é frequentemente usado com o Apache Storm ou o Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (HDInsight versão 3.5 e 3.6) introduziu uma API de streaming que permite que você crie soluções de transmissão sem a necessidade do Storm ou do Spark.

    Para obter mais informações, consulte [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md).

* **Escala horizontal**: O Kafka particiona fluxos entre os nós no cluster HDInsight. Os processos do Consumidor podem ser associados a partições individuais para fornecer balanceamento de carga ao consumir registros.

    Para obter mais informações, consulte [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md).

* **Entrega em ordem**: Dentro de cada partição, os registros são armazenados no fluxo na ordem em que foram recebidos. Ao associar um processo do consumidor por partição, você pode garantir que os registros sejam processados na ordem.

    Para obter mais informações, consulte [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Casos de uso

* **Mensagens**: já que ele oferece suporte ao padrão de mensagem publicar-assinar, o Kafka geralmente é usado como um agente de mensagem.

* **Rastreamento de atividades**: como o Kafka oferece o registro em log ordenado de registros, pode ser usado para rastrear e recriar as atividades. Por exemplo, as ações do usuário em um site ou em um aplicativo.

* **Agregação**: usando processamento de fluxo, você pode agregar informações de fluxos diferentes para combinar e centralizar as informações em dados operacionais.

* **Transformação**: usando processamento de fluxo, você pode combinar e enriquecer dados de vários tópicos de entrada em um ou mais tópicos de saída.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para aprender a usar o Apache Kafka no HDInsight:

* [Início Rápido: Criar Kafka no HDInsight](apache-kafka-get-started.md)

* [Tutorial: Usar o Apache Spark com o Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Tutorial: Usar o Apache Storm com o Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)