---
title: Usar armazenamentos de metadados externos - Azure HDInsight | Microsoft Docs
description: Use armazenamentos de metadados externos com clusters HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: 767a1b8a8213b0139878c82d64639b2ba10b5f4f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

O metastore Hive no HDInsight é uma parte essencial da arquitetura do Hadoop. Um metastore é o repositório central de esquema que pode ser usado por outras ferramentas de acesso de big data, como Spark, Interactive Query (LLAP), Presto ou Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

![Arquitetura de armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight provisiona um metastore para cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:
- Sem custo adicional. O HDInsight provisiona um metastore para cada tipo de cluster sem nenhum custo adicional para você.
- Cada metastore padrão é parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados também são excluídos.
- Você não pode compartilhar o metastore padrão com outros clusters.
- O metastore padrão usa o BD SQL do Azure básico, que tem um limite de 5 DTUs (unidade de transação do banco de dados).
Esse metastore padrão é normalmente usado para cargas de trabalho relativamente simples que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.


## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:
- Você pode especificar seu próprio Banco de Dados SQL do Azure com o metastore.
- O ciclo de vida do metastore não está associado a um ciclo de vida de clusters e, portanto, você pode criar e excluir clusters sem perder metadados. Os metadados, como os esquemas Hive, serão mantidos até mesmo depois de você excluir e recriar o cluster HDInsight.
- Um metastore personalizado permite que você anexe vários clusters e tipos de cluster ao metastore. Por exemplo, um único metastore pode ser compartilhado em clusters Interactive Query, Hive e Spark no HDInsight.
- Você paga o custo de um metastore (Banco de Dados SQL do Azure) de acordo com o nível de desempenho escolhido.
- Você pode expandir o metastore conforme a necessidade.


![Caso de uso do armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um Banco de Dados SQL do Azure criado anteriormente durante a criação do cluster, ou configurar o Banco de Dados SQL depois que o cluster é criado. Essa opção é especificada nas configurações Armazenamento > Metastore na criação de um novo cluster Hadoop, Spark ou Hive interativo do portal do Azure.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Você também pode adicionar outros clusters a um metastore personalizado no portal do Azure ou nas configurações do Ambari (Hive > Avançado)

![Armazenamento de metadados Hive do HDInsight Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Melhores práticas de metastore Hive

Aqui estão algumas melhores práticas gerais de metastore Hive do HDInsight:

- Use um metastore personalizado sempre que possível, pois isso ajuda a separar os recursos de computação (seu cluster em execução) dos metadados (armazenado no metastore).
- Inicie com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.
- Verifique se o metastore do HDInsight criado para uma versão de cluster do HDInsight não é compartilhado entre diferentes versões de cluster do HDInsight. Versões diferentes do Hive usam esquemas diferentes. Por exemplo, você não pode compartilhar um metastore com clusters Hive 1.2 e Hive 2.1.
- Faça backup de seu metastore personalizado periodicamente.
- Mantenha seu metastore e o cluster HDInsight na mesma região.
- Monitore o desempenho do metastore e sua disponibilidade usando ferramentas de monitoramento do Banco de Dados SQL do Azure, como o portal do Azure ou o Azure Log Analytics.

## <a name="oozie-metastore"></a>Metastore do Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop.  O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore também pode fornecer acesso a dados de trabalho do Oozie depois de você excluir o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Próximas etapas

- [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](./hdinsight-hadoop-provision-linux-clusters.md)
