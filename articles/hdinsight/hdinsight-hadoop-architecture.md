---
title: Arquitetura do Hadoop - Azure HDInsight
description: Descreve o armazenamento e processamento do Hadoop em Clusters HDInsight.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: f22cb6a56e0ef81e3d7799b38e33113f8b175457
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699423"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Arquitetura de Hadoop no HDInsight

O Hadoop inclui dois componentes principais: o HDFS (Sistema de Arquivos Distribuído Hadoop), que fornece armazenamento, e YARN (Yet Another Resource Negotiator), que fornece processamento. Com capacidades de armazenamento e processamento, um cluster torna-se capaz de executar programas MapReduce para executar o processamento de dados desejado.

> [!NOTE]
> Normalmente, um HDFS não é implantado no Cluster HDInsight para fornecer armazenamento. Em vez disso, uma camada de interface compatível com HDFS é utilizada pelos componentes do Hadoop. A capacidade de armazenamento real é fornecida pelo Armazenamento do Microsoft Azure ou pelo Azure Data Lake Store. Para o Hadoop, os trabalhos MapReduce executados no Cluster HDInsight funcionam como se um HDFS estivesse presente e, portanto, não exigem alterações para dar suporte às necessidades de armazenamento. No Hadoop, no HDInsight, o armazenamento é terceirizado, mas o processamento YARN continua sendo um componente principal. Para obter mais informações, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Este artigo apresenta o YARN e como ele coordena a execução de aplicativos no HDInsight.

## <a name="yarn-basics"></a>Noções básicas do YARN 

O YARN rege e coordena o processamento de dados em Hadoop. O YARN possui dois serviços principais que são executados como processos em nós no cluster: 

* ResourceManager 
* NodeManager

O ResourceManager concede recursos de computação de cluster para aplicativos como trabalhos MapReduce. O ResourceManager concede esses recursos como contêineres, onde cada contêiner consiste de uma alocação de núcleos de CPU e memória RAM. Se você combinou todos os recursos disponíveis em um cluster e distribuiu os núcleos e a memória em blocos, cada bloco de recursos será um contêiner. Cada nó no cluster tem uma capacidade para um determinado número de contêineres, portanto, o cluster tem um limite fixo no número de contêineres disponíveis. A alocação de recursos em um contêiner é configurável. 

Quando um aplicativo MapReduce é executado em um cluster, o ResourceManager fornece ao aplicativo os contêineres a executar. O ResourceManager rastreia o status de aplicativos em execução, a capacidade de cluster disponível e rastreia aplicativos conforme conclui e libera seus recursos. 

O ResourceManager também executa um processo do servidor Web que fornece uma interface do usuário da Web para monitorar o status dos aplicativos.

Quando um usuário envia um aplicativo MapReduce para ser executado no cluster, o aplicativo é submetido ao ResourceManager. Por sua vez, o ResourceManager aloca um contêiner nos nós NodeManager disponíveis. Os nós NodeManager são onde o aplicativo realmente executa. O primeiro contêiner alocado executa um aplicativo especial chamado ApplicationMaster. Esse ApplicationMaster é responsável pela aquisição de recursos, sob a forma de contêineres subsequentes, necessários para executar o aplicativo enviado. O ApplicationMaster examina os estágios do aplicativo, como o estágio do mapa e o estágio de redução, e os fatores em quantos dados precisam ser processados. Em seguida, o ApplicationMaster solicita (*negocia*) os recursos do ResourceManager em nome do aplicativo. O ResourceManager, por sua vez, concede recursos do NodeManagers no cluster ao ApplicationMaster para que ele seja usado na execução do aplicativo. 

O NodeManagers executa as tarefas que compõem o aplicativo e, em seguida, relata seu progresso e status ao ApplicationMaster. O ApplicationMaster, por sua vez, relata o status do aplicativo de volta ao ResourceManager. O ResourceManager retorna quaisquer resultados para o cliente.

## <a name="yarn-on-hdinsight"></a>YARN no HDInsight

Todos os tipos de Cluster HDInsight implantam o YARN. O ResourceManager é implantado para alta disponibilidade com uma instância primária e secundária, que é executada no primeiro e no segundo nós principais no cluster, respectivamente. Apenas uma instância do ResourceManager é ativa por vez. As instâncias do NodeManager são executadas nos nós de trabalho disponíveis no cluster.

![YARN no HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Próximas etapas

* [Usar o MapReduce no Hadoop do HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
