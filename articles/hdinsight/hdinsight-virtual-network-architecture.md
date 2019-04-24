---
title: Arquitetura de rede virtual do Azure HDInsight
description: Aprenda os recursos disponíveis quando você cria um cluster HDInsight em uma rede Virtual do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
origin.date: 03/26/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: 6d92273298c0448d7377acab6f3b8ea1cc1ed908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484864"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual do Azure HDInsight

Este artigo explica os recursos que estão presentes quando você implanta um cluster do HDInsight em uma rede Virtual personalizada do Azure. Essas informações o ajudará a se conectar a recursos locais para seu cluster HDInsight no Azure. Para obter mais informações sobre redes virtuais do Azure, consulte [qual é a rede Virtual do Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recurso em clusters de HDInsight do Azure

Clusters de HDInsight do Azure têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha um papel na operação do sistema. A tabela a seguir resume esses tipos de nós e suas funções no cluster.

| Type | DESCRIÇÃO |
| --- | --- |
| Nó de cabeçalho |  Para todos os tipos de cluster, exceto o Apache Storm, nós de cabeçalho do hospedam os processos que gerenciar a execução do aplicativo distribuído. O nó principal também é o que você pode SSH no nó e executar aplicativos que são coordenados, em seguida, para executar entre os recursos de cluster. O número de nós de cabeçalho é fixo em dois para todos os tipos de cluster. |
| Nó do zooKeeper | Zookeeper coordena tarefas entre os nós que estão fazendo o processamento de dados. Ele também faz eleição de líder do nó principal e mantém o controle de qual nó principal está em execução em um serviço mestre específico. O número de nós do ZooKeeper é fixo em dois. |
| Nó de trabalho | Representa os nós que dão suporte à funcionalidade de processamento de dados. Nós de trabalho podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar os custos. |
| Nó de borda do R Server | O nó de borda do R Server representa o nó que você pode realizar o SSH em e executar aplicativos que são coordenados, em seguida, para executar entre os recursos de cluster. Um nó de borda não participa de análise de dados dentro do cluster. Esse nó também hospeda o R Studio Server, permitindo que você execute o aplicativo de R usando um navegador. |
| Nó de região | Para o tipo de cluster HBase, o nó de região (também conhecido como um nó de dados) executa o servidor de região. Servidores de região servem e gerenciar uma parte dos dados gerenciados pelo HBase. Nós de região podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar os custos.|
| Nó Nimbus | Para o tipo de cluster Storm, o nó Nimbus fornece uma funcionalidade semelhante para o nó principal. O nó Nimbus atribui tarefas a outros nós em um cluster por meio do Zookeeper, que coordena a execução de topologias Storm. |
| Nó Supervisor | Para o tipo de cluster Storm, o nó supervisor executa as instruções fornecidas pelo nó do Nimbus para executar o processamento desejado. |

## <a name="basic-virtual-network-resources"></a>Recursos de rede virtual básica

O diagrama a seguir mostra o posicionamento de nós do HDInsight e recursos de rede no Azure.

![Diagrama de entidades do HDInsight criado na VNET personalizado do Azure](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Os recursos padrão presentes quando HDInsight é implantado em uma rede Virtual do Azure incluem os tipos de nós de cluster mencionados na tabela anterior, bem como dispositivos de rede que dão suporte a comunicação entre a rede virtual e fora de redes.

A tabela a seguir resume os nós de cluster de nove que são criados quando HDInsight é implantado em uma rede Virtual personalizada do Azure.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó de cabeçalho | dois |    |
|Nó do Zookeeper | três | |
|Nó de trabalho | dois | Esse número pode variar com base na configuração de cluster e dimensionamento. É necessário um mínimo de três nós de trabalho do Apache Kafka.  |
|Nó de gateway | dois | Nós de gateway são máquinas virtuais do Azure que são criadas no Azure, mas não são visíveis em sua assinatura. Contate o suporte se você precisar reinicializar esses nós. |

Os seguintes recursos de rede presentes são criados automaticamente dentro da rede virtual usada com o HDInsight:

| Recursos de rede | Número presente | Detalhes |
| --- | --- | --- |
|Balanceador de carga | três | |
|Interfaces de Rede | nove | Esse valor é baseado em um cluster normal, onde cada nó tem sua própria interface de rede. As interfaces de nove são para os dois nós de cabeçalho, três nós zookeeper, dois nós de trabalho e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos de extremidade para se conectar ao HDInsight

Você pode acessar seu cluster HDInsight de três maneiras:

- Um ponto de extremidade HTTPS fora da rede virtual em `CLUSTERNAME.azurehdinsight.net`.
- Um ponto de extremidade do SSH para conectar-se diretamente ao nó principal em `CLUSTERNAME-ssh.azurehdinsight.net`.
- Um ponto de extremidade HTTPS na rede virtual `CLUSTERNAME-int.azurehdinsight.net`. Observe o "-int" nessa URL. Esse ponto de extremidade será resolvido para um endereço IP privado na rede virtual e não está acessível pela internet pública.

Esses três pontos de extremidade são atribuídos um balanceador de carga.

Endereços IP públicos também são fornecidos para os dois pontos de extremidade que permitem a conexão de fora da rede virtual.

1. Um IP público é atribuído ao balanceador de carga para o nome de domínio totalmente qualificado (FQDN) para usar ao se conectar ao cluster pela internet `CLUSTERNAME.azurehdinsight.net`.
1. O segundo endereço IP público é usado para o nome de domínio somente de SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Próximas etapas

* [Proteger o tráfego de entrada para clusters do HDInsight em uma rede virtual com pontos de extremidade privados](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
