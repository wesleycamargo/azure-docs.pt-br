---
title: Alta disponibilidade com o Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Saiba como garantir a alta disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que eles fiquem em domínios de falha diferentes dentro da região do Azure que contém o HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f8164d1c3483b28e5f2abcc8035da78880daec1e
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017

---
# Alta disponibilidade de seus dados com o Apache Kafka (versão prévia) no HDInsight
<a id="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight" class="xliff"></a>

Saiba como configurar réplicas da partição para tópicos Kafka a fim de tirar proveito da configuração de rack do hardware subjacente. Essa configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

## Domínios de falha e atualização com Kafka
<a id="fault-and-update-domains-with-kafka" class="xliff"></a>

Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. As máquinas virtuais e os discos gerenciados que implementam os nós em um cluster HDInsight são distribuídos entre esses domínios de falha. Essa arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e a quantidade de domínios de falha que eles contêm, confira a documentação [conjuntos de disponibilidade](../virtual-machines/linux/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> O Kafka não está ciente dos domínios de falha. Quando você cria um tópico no Kafka, ele pode armazenar todas as réplicas da partição no mesmo domínio de falha. Para resolver esse problema, fornecemos a [ferramenta de rebalanceamento de partição do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## Quando rebalancear réplicas da partição
<a id="when-to-rebalance-partition-replicas" class="xliff"></a>

Para garantir a mais alta disponibilidade de seus dados do Kafka, você deve rebalancear as réplicas de partição do tópico nas seguintes situações:

* Quando um novo tópico ou uma partição é criado

* Quando você expande um cluster

## Fator de replicação
<a id="replication-factor" class="xliff"></a>

> [!IMPORTANT]
> É recomendável usar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se você precisa usar uma região que contém apenas dois domínios de falha, use um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

Para obter um exemplo como criar tópicos e configurar o fator de replicação, consulte o documento [Introdução ao Kafka no HDInsight](hdinsight-apache-kafka-get-started.md).

## Como rebalancear réplicas da partição
<a id="how-to-rebalance-partition-replicas" class="xliff"></a>

Use a [ferramenta de rebalanceamento de partição do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para rebalancear tópicos selecionados. Essa ferramenta deve ser executada em uma sessão SSH para o nó principal do cluster Kafka.

Para saber mais sobre como se conectar ao HDInsight usando SSH, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## Próximas etapas
<a id="next-steps" class="xliff"></a>

* [Escalabilidade do Kafka no HDInsight](hdinsight-apache-kafka-scalability.md)
* [Espelhamento com o Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)
