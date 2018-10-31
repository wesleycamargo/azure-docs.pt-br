---
title: Melhorar o desempenho de cargas de trabalho do Apache Spark usando o Cache de E/S do Azure HDInsight (versão prévia)
description: Saiba mais sobre o Cache de E/S do Azure HDInsight e sobre como usá-lo para melhorar o desempenho do Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 3616183b5ea34b8a14712d2c449de87950443111
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954497"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Melhorar o desempenho de cargas de trabalho do Apache Spark usando o Cache de E/S do Azure HDInsight (versão prévia)

O Cache de E/S é um serviço de cache de dados para o Azure HDInsight que melhora o desempenho de trabalhos do Apache Spark. O Cache de E/S também funciona com cargas de trabalho do Tez e do Hive, que podem ser executadas em clusters do Spark. O Cache de E/S usa um componente de cache de software livre chamado RubiX. O RubiX é um cache de disco local para uso com mecanismos de análise de big data que acessam dados de sistemas de armazenamento de nuvem. O RubiX é exclusivo entre os sistemas de cache porque usa unidades de estado sólido (SSDs) em vez de reservar memória operacional para fins de cache. O serviço de Cache de E/S é iniciado e gerencia Servidores de Metadados do RubiX nó de trabalho do cluster. Ele também configura todos os serviços do cluster para uso transparente do cache do RubiX.

A maioria dos SSDs fornece mais de 1 GB por segundo de largura de banda. Essa largura de banda, complementada pelo cache de arquivo na memória do sistema operacional, fornece largura de banda suficiente para carregar os mecanismos de processamento de computação de big data, como o Apache Spark. A memória operacional fica disponível para o Apache Spark processar tarefas altamente dependentes da memória, como as ordens aleatórias. Ter o uso exclusivo da memória operacional permite que o Apache Spark atinja o uso ideal de recursos.  

>[!Note]
>Atualmente, o Cache de E/S usa o RubiX como um componente de cache, mas isso pode mudar em futuras versões do serviço. Use interfaces de Cache de E/S e não obtenha qualquer dependência diretamente na implementação do RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios do Cache de E/S do Azure HDInsight

Usar o Cache de E/S fornece um aumento de desempenho para trabalhos que leem dados do Armazenamento de Blobs do Azure.

Você não precisa fazer nenhuma alteração em seus trabalhos do Spark para ver os aumentos de desempenho ao usar o Cache de E/S. Quando o Cache de E/S é desabilitado, esse código Spark leria os dados remotamente do Armazenamento de Blobs do Azure: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Quando o Cache de E/S é ativado, a mesma linha de código causa uma leitura em cache por meio do Cache de E/S. Nas leituras a seguir, os dados são lidos localmente do SSD. Os nós de trabalho no cluster HDInsight são equipados com unidades SSD anexadas localmente e dedicadas. O Cache de E/S do HDInsight usa esse SSDs locais para armazenar em cache, o que fornece um nível mais baixo de latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

O Cache de E/S do Azure HDInsight está desativado por padrão na versão prévia. O Cache de E/S está disponível em clusters Spark do Azure HDInsight 3.6+, que executam o Apache Spark 2.3.  Para ativar o Cache de E/S, faça o seguinte:

1. Selecione o cluster HDInsight no [portal do Azure](https://portal.azure.com).

1. Na página **Visão geral** (aberta por padrão quando você seleciona o cluster), selecione **Página inicial do Ambari** em **Painéis do Cluster**.

1. Selecione o serviço **Cache de E/S** à esquerda.

1. Selecione **Ações** e **Ativar**.

    ![Habilitar o serviço de Cache de E/S no Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Habilitar o serviço de Cache de E/S no Ambari")

1. Confirme a reinicialização de todos os serviços afetados no cluster.

>[!NOTE] 
> Mesmo que a barra de progresso mostre ativado, o Cache de E/S, na verdade, não estará habilitado até que você reinicie os outros serviços afetados.

## <a name="troubleshooting"></a>solução de problemas
  
Você pode receber erros de espaço em disco ao executar trabalhos do Spark depois de habilitar o Cache de E/S. Esses erros acontecem porque o Spark também usa o armazenamento de disco local para armazenar dados durante operações de embaralhamento. O Spark poderá ocupar todo o espaço do SSD, já que o Cache de E/S está habilitado e o espaço para armazenamento do Spark é reduzido. A quantidade de espaço padrão usado pelo Cache de E/S é de metade do espaço total do SSD. O uso de espaço em disco para o Cache de E/S é configurável no Ambari. Se você receber erros de espaço em disco, reduza a quantidade de espaço de SSD usada pelo Cache de E/S e reinicie o serviço. Para alterar o espaço definido para o Cache de E/S, execute as seguintes etapas:

1. No Ambari, selecione o serviço **HDFS** à esquerda.

1. Selecione as guias **Configurações** e **Avançado**.

    ![Editar Configuração Avançada do HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Editar Configuração Avançada do HDFS")

1. Role para baixo e expanda a área **Site principal personalizado**.

1. Localize a propriedade **hadoop.cache.data.fullness.percentage**.

1. Altere o valor na caixa.

    ![Editar o Percentual de Preenchimento do Cache de E/S](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Editar o Percentual de Preenchimento do Cache de E/S")

1. Selecione **Salvar** no canto superior direito.

1. Selecione **Reiniciar** > **Reiniciar Todos os Afetados**.

    ![Reiniciar todos os afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Reiniciar todos os afetados")

1. Selecione **Confirmar Reiniciar Tudo**.

Se isso não funcionar, desabilite o Cache de E/S.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o Cache de E/S, incluindo os benchmarks de desempenho nesta postagem no blog: [Trabalhos do Apache Spark ficam até nove vezes mais rápidos com o Cache de E/S do HDInsight](https://azure.microsoft.com/en-us/blog/apache-spark-speedup-with-hdinsight-io-cache/)
