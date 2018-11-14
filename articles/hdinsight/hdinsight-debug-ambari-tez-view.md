---
title: Use o Apache Ambari Tez View com o HDInsight - Azure
description: Aprenda a usar a visualização Apache Ambari Tez para depurar tarefas do Tez no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c85ad59acc8e307de05f41365855f3a9669ac2b5
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034669"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Use o Apache Ambari Views para depurar os trabalhos do Apache Tez no HDInsight

A UI do Apache Ambari para o HDInsight contém uma visualização do Apache Tez que pode ser usada para reconhecer e depurar tarefas que usam o Tez. O modo de exibição do Tez permite que você visualize o trabalho como um grafo de itens conectados, detalhe cada item e recupere estatísticas e informações de log.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para ver as etapas para criar um cluster, consulte [Introdução ao HDInsight baseado no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Um navegador da Web moderno, com suporte a HTML5.

## <a name="understanding-apache-tez"></a>Noções básicas sobre o Apache Tez

O Tez é uma estrutura extensível para processamento de dados no Apache Hadoop que fornece velocidades maiores do que o processamento tradicional do MapReduce. Para clusters HDInsight baseados em Linux, é o mecanismo padrão para Hive.

O Tez cria um DAG (grafo direcionado acíclico) que descreve a ordem das ações necessárias ao trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

### <a name="understanding-the-tez-view"></a>Noções básicas do layout da interface de usuário do Tez

A exibição do Tez fornece informações históricas e sobre os processos em execução. Essas informações mostram como um trabalho é distribuído entre os clusters. Ele também exibe contadores usados por tarefas e vértices, bem como informações de erro relacionadas ao trabalho. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.
* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

## <a name="generate-a-dag"></a>Gerar um DAG

A exibição do Tez só conterá dados se um trabalho que usa o mecanismo Tez estiver sendo executado ou se já foi executado anteriormente. Consultas de Hive simples podem ser resolvidas sem usar o Tez. Consultas mais complexas que executam filtragem, agrupamento, ordenação, associações, etc. usam o mecanismo Tez.

Use as etapas a seguir para executar uma consulta do Hive que usa o Tez:

1. Em um navegador da Web, navegue para https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster HDInsight.

2. No menu na parte superior da página, escolha o ícone **Exibições**. Esse ícone é semelhante a uma série de quadrados. No menu suspenso que aparece, escolha **Exibição do Hive**.

    ![Como selecionar o modo de exibição do Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando a exibição do Hive for carregada, cole a seguinte consulta no Editor de Consulta e clique em **Executar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Após a conclusão do trabalho, você deverá ver a saída exibida na seção **Resultados do Processo de Consulta**. Os resultados devem ser semelhantes ao seguinte texto:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Selecione a guia **Log**. Você verá informações semelhantes ao seguinte texto:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Salve o valor de **ID do aplicativo**, pois ele será usado na próxima seção.

## <a name="use-the-tez-view"></a>Usar o modo de exibição do Tez

1. No menu na parte superior da página, escolha o ícone **Exibições**. Na lista suspensa que aparece, escolha **Exibição do Tez**.

    ![Como selecionar o modo de exibição do Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando a exibição do Tez for carregada, você verá uma lista de consultas do Hive que estão em execução no momento ou que foram executadas no cluster.

    ![Todos os DAGS](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Se você tiver apenas uma entrada, ela representará a consulta que você executou na seção anterior. Se tiver várias entradas, você poderá pesquisar usando os campos na parte superior da página.

4. Selecione a **ID da consulta** para uma consulta do Hive. As informações sobre a consulta são exibidas.

    ![Detalhes do DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. As guias desta página permitem que você exiba as seguintes informações:

    * **Detalhes da consulta**: os detalhes sobre a consulta do Hive.
    * **Linha do tempo**: as informações sobre quanto tempo durou cada estágio de processamento.
    * **Configurações**: a configuração usada nesta consulta.

    Em __Detalhes da consulta__, você pode usar os links para encontrar mais informações sobre o __Aplicativo__ ou o __DAG__ para esta consulta.
    
    * O link __Aplicativo__ exibe informações sobre o aplicativo YARN para esta consulta. Aqui você pode acessar os logs de aplicativo YARN.
    * O link __DAG__ exibe informações sobre o grafo direcionado acíclico para esta consulta. Aqui você pode exibir uma representação gráfica do DAG. Você também pode encontrar informações sobre os vértices no DAG.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o modo de exibição do Tez, saiba mais sobre [Como usar o Hive no HDInsight](hadoop/hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, confira a [página sobre o Tez em Hortonworks](http://hortonworks.com/hadoop/tez/).

Para saber mais sobre como usar o Ambari com o HDInsight, confira [Gerenciar clusters HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md)
