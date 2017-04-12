---
title: "Usar a Exibição do Ambari Tez com HDInsight | Microsoft Docs"
description: "Saiba como usar o modo de exibição do Ambari Tez para depurar trabalhos do Tez no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 7654875c7b6384943d733de2d0097082270d64b4
ms.lasthandoff: 04/11/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usar os modos de exibição do Ambari para depurar trabalhos do Tez no HDInsight
A interface de usuário da Web do Ambari para HDInsight contém um modo de exibição do Tez que pode ser usado para entender e depurar trabalhos que usam Tez como o mecanismo de execução. O modo de exibição do Tez permite que você visualize o trabalho como um gráfico de itens conectados, detalhe cada item e recupere estatísticas e informações de log.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos
* Criar um cluster HDInsight baseado em Linux. Para saber as etapas para criação de um novo cluster, confira [Introdução ao HDInsight baseado no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
* Um navegador da Web moderno, com suporte a HTML5.

## <a name="understanding-tez"></a>Noções básicas sobre o Tez
Tez é uma estrutura extensível para processamento de dados no Hadoop que fornece maior velocidade de processamento do que o MapReduce tradicional. Para clusters HDInsight baseados em Linux, é o mecanismo padrão para Hive.

Quando o trabalho é enviado ao Tez, ele cria um DAG (Gráfico Acíclico Dirigido) que descreve a ordem de execução das ações exigidas pelo trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

### <a name="understanding-the-tez-view"></a>Noções básicas do layout da interface de usuário do Tez
O modo de exibição do Tez fornece informações sobre processos em execução, ou que foram executados anteriormente com o Tez. Ele permite exibir o DAG gerado pelo Tez, como ele é distribuído entre os clusters, contadores, como a memória usada por tarefas e vértices, além de informações de erro. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.
* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

## <a name="generate-a-dag"></a>Gerar um DAG
O modo de exibição do Tez só conterá dados se um trabalho que use o mecanismo Tez estiver sendo executado ou se tiver sido executado anteriormente. Normalmente, as consultas simples do Hive podem ser resolvidas sem usar o Tez. Porém, as consultas mais complexas que realizam filtragem, agrupamento, ordenação, associações etc. normalmente exigirão o Tez.

Use as etapas a seguir para executar uma consulta do Hive usando o Tez.

1. Abra um navegador da Web, navegue para https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do seu cluster HDInsight.
2. No menu na parte superior da página, escolha o ícone **Exibições**. Isso é semelhante uma série de quadrados. No menu suspenso que aparece, escolha **Exibição do Hive**.

    ![Como selecionar o modo de exibição do Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)
3. Quando a exibição do Hive carregar, cole o seguinte no Editor de Consulta e clique em **Executar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Após a conclusão do trabalho, você deverá ver a saída exibida na seção **Resultados do Processo de Consulta**. Os resultados devem ser semelhantes ao seguinte:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
4. Selecione a guia **Log**. Você verá informações parecidas com as seguintes:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Salve o valor de **Id do aplicativo**, pois ele será usado na próxima seção.

## <a name="use-the-tez-view"></a>Usar o modo de exibição do Tez
1. No menu na parte superior da página, escolha o ícone **Exibições**. Na lista suspensa que aparece, escolha **Exibição do Tez**.

    ![Como selecionar o modo de exibição do Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)
2. Quando o modo de exibição do Tez for carregado, você verá uma lista de DAGs atualmente em execução ou que foram executados no cluster. O modo de exibição padrão inclui Nome, Id, Remetente, Status, Hora de Início, Hora de Término, Duração, ID do Aplicativo e Fila do Dag. É possível adicionar mais colunas usando o ícone de engrenagem no canto direito da página.

    ![Todos os DAGS](./media/hdinsight-debug-ambari-tez-view/alldags.png)
3. Se você tiver apenas uma entrada, ela será para a consulta que você executou na seção anterior. Se você tiver várias entradas, poderá pesquisar inserindo a ID do aplicativo no campo **ID do Aplicativo**, então, pressione Enter.
4. Selecione o **Nome do Dag**. Isso exibirá informações sobre o DAG, bem como a opção para baixar um zip de arquivos JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)
5. Acima dos **Detalhes do DAG**, há vários links que podem ser usados para exibir informações sobre o DAG.

   * **Contadores de DAG** exibe informações de contadores do DAG em questão.
   * **Modo de Exibição Gráfico** exibe uma representação gráfica deste DAG.
   * **Todos os Vértices** exibe uma lista dos vértices neste DAG.
   * **Todas as Tarefas** exibe uma lista das tarefas de todos os vértices neste DAG.
   * **Todas as Tentativas de Tarefa** exibe informações sobre as tentativas de execução de tarefas para este DAG.

     > [!NOTE]
     > Se você rolar a exibição da coluna até Vértices, Tarefas e Tentativas de Tarefa, observe que há links para exibir **contadores** e **exibir ou baixar logs** para cada linha.
     >
     >

     Se houver uma falha com o trabalho, os Detalhes do DAG exibirão um status de FALHA, juntamente com links para informações sobre a tarefa com falha. As informações de diagnóstico serão exibidas abaixo dos detalhes do DAG.

     ![Uma tela de Detalhes do DAG detalhando uma falha](./media/hdinsight-debug-ambari-tez-view/faileddag.png)
6. Selecione **Exibição Gráfica**. Isso exibe uma representação gráfica do DAG. Você pode colocar o mouse sobre cada vértice no modo de exibição para exibir informações sobre ele.

    ![Modo de Exibição Gráfico](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)
7. Clicar em um vértice carregará os **Detalhes do Vértice** para esse item. Clique no vértice **Mapa 1** para exibir os detalhes desse item.

    ![Detalhes do Vértice](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)
8. Observe que agora você tem links relacionados às tarefas e aos vértices na parte superior da página.

   > [!NOTE]
   > Você também pode chegar a essa página voltando para **Detalhes do DAG**, selecionando **Detalhes do Vértice** e selecionando o vértice **Mapa 1**.
   >
   >

   * **Contadores de Vértice** exibe informações de contadores do vértice em questão.
   * **Tarefas** exibe tarefas do vértice em questão.
   * **Tentativas de Tarefa** exibe informações sobre as tentativas de execução de tarefas do vértice em questão.
   * **Fontes e Coletores** exibe fontes de dados e coletores do vértice em questão.

     > [!NOTE]
     > Assim como no menu anterior, você pode rolar a exibição da coluna para Tarefas, Tentativas de Tarefa e Fontes de Coletores para exibir links para outras informações sobre cada item.
     >
     >
9. Escolha **Tarefas** e selecione o item chamado **00_000000**. Isso exibirá os **Detalhes da Tarefa** desta tarefa. Nessa tela, você pode exibir **Contadores de Tarefa** e **Tentativas de Tarefa**.

   ![Detalhes de tarefa](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a usar o modo de exibição do Tez, saiba mais sobre [Como usar o Hive no HDInsight](hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, confira a [página sobre o Tez em Hortonworks](http://hortonworks.com/hadoop/tez/).

Para saber mais sobre como usar o Ambari com o HDInsight, confira [Gerenciar clusters HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md)

