---
title: Otimização de desempenho para clusters do Apache Kafka HDInsight
description: Fornece uma visão geral das técnicas para otimizar cargas de trabalho do Apache Kafka no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 903cd8921801ffb47dd73f48e507f30aa0b6dccc
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373144"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para clusters do Apache Kafka HDInsight

Este artigo fornece algumas sugestões para otimizar o desempenho de suas cargas de trabalho do Apache Kafka no HDInsight. O foco é sobre o ajuste de produtor e configuração do agente. Há diferentes maneiras de medir o desempenho e as otimizações que você aplicar dependerá de suas necessidades de negócios.

## <a name="architecture-overview"></a>Visão geral da arquitetura

Os tópicos do Kafka são usados para organizar os registros. Registros são produzidos por produtores e consumidos pelos consumidores. Produtores de enviam os registros para agentes do Kafka, que, em seguida, armazenam os dados. Cada nó de trabalho no cluster HDInsight é um agente do Kafka.

Registros de partição de tópicos entre agentes. Ao consumir registros, você pode usar um consumidor por partição a fim de alcançar o processamento paralelo dos dados.

A replicação é usada para duplicar as partições entre os nós. Isso protege contra falhas do nó (agente). Uma única partição entre o grupo de réplicas é designada como o líder de partição. O produtor tráfego é roteado para o preenchimento de cada nó, usando o estado gerenciado pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar seu cenário

Desempenho do Apache Kafka tem dois aspectos principais – taxa de transferência e latência. Taxa de transferência é a taxa máxima em que os dados podem ser processados. Maior taxa de transferência é geralmente melhor. Latência é o tempo necessário para os dados a ser armazenado ou recuperado. Latência mais baixa é geralmente melhor. Encontrar o equilíbrio certo entre a taxa de transferência, latência e o custo da infraestrutura do aplicativo pode ser um desafio. Seus requisitos de desempenho provavelmente corresponderá a uma das situações a seguir três comuns, com base em se você precisar de alta taxa de transferência, baixa latência ou ambos:

* Alta taxa de transferência, baixa latência. Esse cenário requer alta taxa de transferência e baixa latência (~ 100 milissegundos). Um exemplo desse tipo de aplicativo é serviço de monitoramento de disponibilidade.
* Alta taxa de transferência, alta latência. Esse cenário requer alta taxa de transferência (~1.5 GBps), mas pode tolerar a latência mais alta (< 250 ms). Um exemplo desse tipo de aplicativo é a ingestão de dados de telemetria para perto de processos em tempo real, como aplicativos de detecção de intrusão e segurança.
* Baixa taxa de transferência, baixa latência. Esse cenário requer baixa latência (< 10 ms) para processamento em tempo real, mas pode tolerar a taxa de transferência menor. Um exemplo desse tipo de aplicativo é verificações online de ortografia e gramática.

## <a name="producer-configurations"></a>Configurações de produtor

As seções a seguir destacar algumas das propriedades de configuração mais importantes para otimizar o desempenho de seu produtores do Kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte [documentação do Apache Kafka em configurações de produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamanho do lote

Produtores do Apache Kafka montam os grupos de mensagens (chamados de lotes) que são enviadas como uma unidade a ser armazenado em uma partição única de armazenamento. Tamanho do lote significa o número de bytes que devem estar presentes antes desse grupo é transmitido. Aumentando o `batch.size` parâmetro pode aumentar a taxa de transferência, porque reduz o processamento de sobrecarga de rede e as solicitações de e/s. Sob carga leve, tamanho do lote maior poderá aumentar a latência de envio de Kafka como o produtor aguarda um lote para estar pronto. Sob carga pesada, é recomendável aumentar o tamanho de lote para melhorar a taxa de transferência e latência.

### <a name="producer-required-acknowledgements"></a>Confirmações de produtor necessária

O produtor necessário `acks` configuração determina o número de confirmações exigido pelo líder de partição antes de uma solicitação de gravação é considerada concluído. Essa configuração afeta a confiabilidade de dados e ele recebe valores de `0`, `1`, ou `-1`. O valor de `-1` significa que uma confirmação deve ser recebida de todas as réplicas antes que a gravação seja concluída. Configuração `acks = -1` oferece garantias mais fortes contra perda de dados, mas também os resultados no maior latência e taxa de transferência menor. Se seus requisitos de aplicativo exigem maior taxa de transferência, tente definir `acks = 0` ou `acks = 1`. Tenha em mente, o que pode reduzir a confiabilidade de dados não confirmando todas as réplicas.

### <a name="compression"></a>Compactação

Um produtor Kafka pode ser configurado para compactar mensagens antes de enviá-los para os agentes. O `compression.type` configuração especifica o codec de compactação a ser usado. Codecs de compactação com suporte são "gzip," "interessantes" e "lz4." A compactação é benéfica e deve ser considerada se há uma limitação na capacidade do disco.

Entre os dois codecs de compactação comumente usados, `gzip` e `snappy`, `gzip` tem uma maior taxa de compactação, o que resulta no uso do disco inferior ao custo de carga de CPU maior. O `snappy` codec fornece menor compactação com menos sobrecarga de CPU. Você pode decidir que codec para usar com base nas limitações de CPU de disco ou o produtor de agente. `gzip` pode compactar dados em uma taxa de cinco vezes maior do que `snappy`.

Usar a compactação de dados aumentará o número de registros que podem ser armazenados em um disco. Ele também pode aumentar CPU sobrecarga nos casos em que há uma incompatibilidade entre os formatos de compactação que está sendo usados pelo produtor e o agente. como os dados devem ser compactados antes de enviar e, em seguida, descompactados antes do processamento.

## <a name="broker-settings"></a>Configurações do agente

As seções a seguir destacar algumas das configurações mais importantes para otimizar o desempenho dos seus agentes de Kafka. Para obter uma explicação detalhada de todas as configurações do agente, consulte [documentação do Apache Kafka em configurações de produtor](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Número de discos

Discos de armazenamento têm limitado o IOPS (entrada/saída operações por segundo) e leitura/gravação de bytes por segundo. Ao criar novas partições, o Kafka armazena cada nova partição no disco com o menor número de partições existentes para balanceá-los entre os discos disponíveis. Apesar de estratégia de armazenamento, ao processar centenas de réplicas da partição em cada disco, o Kafka pode esgotar facilmente a taxa de transferência de disco disponível. Aqui, a compensação está entre a taxa de transferência e o custo. Se seu aplicativo exigir maior taxa de transferência, criar um cluster com mais gerenciados discos por agente. HDInsight não suporta no momento, a adição de discos gerenciados em um cluster em execução. Para obter mais informações sobre como configurar o número de discos gerenciados, consulte [configurar o armazenamento e a escalabilidade do Apache Kafka no HDInsight](apache-kafka-scalability.md). Entenda as implicações de custo de cada vez maior espaço de armazenamento para os nós no cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e partições

Os produtores de Kafka gravam tópicos. Consumidores do Kafka ler de tópicos. Um tópico é associado um log, que é uma estrutura de dados no disco. Kafka acrescenta os registros de um producer(s) para o final do log de um tópico. Um log de tópico consiste em várias partições são distribuídas por vários arquivos. Esses arquivos são, por sua vez, distribuídos em vários nós de cluster do Kafka. Os consumidores leem de tópicos do Kafka em uma cadência de seu e e pode escolher sua posição (deslocamento) no log de tópico.

Cada partição do Kafka é um arquivo de log do sistema e threads de produtor podem gravar simultaneamente em vários logs. Da mesma forma, uma vez que cada thread de consumidor lê as mensagens de uma partição, consumo de várias partições é tratada em paralelo também.

Aumentar a densidade de partição (o número de partições por agente) adiciona uma sobrecarga para operações de metadados e por partição solicitação/resposta entre o líder de partição e de seus seguidores relacionada. Mesmo na ausência de dados que fluem por meio do, as réplicas de partição ainda buscam dados dos líderes, que resulta em processamento extra para enviar e receber solicitações pela rede.

Clusters de 1.1 para o Apache Kafka e acima no HDInsight, é recomendável ter um máximo de 1.000 partições por agente, incluindo réplicas. Aumentar o número de partições por broker diminui a produtividade e também pode causar a indisponibilidade do tópico. Para obter mais informações sobre o suporte de partição do Kafka, consulte [a postagem de blog do Apache Kafka oficial sobre o aumento no número de partições com suporte na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para obter detalhes sobre como modificar tópicos, consulte [Apache Kafka: modificando tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

Fator de replicação mais alto resulta em solicitações adicionais entre o líder de partição e seguidores. Consequentemente, um fator de replicação mais alto consome mais disco e CPU para lidar com solicitações adicionais, aumentando a gravação de latência e diminuir a taxa de transferência.

É recomendável que você use a replicação de x pelo menos 3 para o Kafka no HDInsight do Azure. Regiões do Azure mais tem três domínios de falha, mas em regiões com apenas dois domínios de falha, os usuários devem usar a replicação de 4 x.

Para obter mais informações sobre replicação, consulte [Apache Kafka: replicação](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: aumentando o fator de replicação](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Próximas etapas

* [Processando trilhões de eventos por dia com o Apache Kafka no Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
