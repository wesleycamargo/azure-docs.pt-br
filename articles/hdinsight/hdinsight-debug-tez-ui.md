---
title: Use a interface do usuário do Apache Tez com o HDInsight baseado em Windows - Azure
description: Aprenda a usar a interface do usuário do Apache Tez para depurar tarefas do Tez no HDInsight baseado em Windows.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 1e529b2276d2e68c67696ba9d142760f5881a25e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012803"
---
# <a name="use-the-apache-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Use a interface do usuário do Apache Tez para depurar tarefas do Tez no HDInsight baseado em Windows
A interface do usuário do [Apache TEZ](https://tez.apache.org/) pode ser usada para depurar tarefas do [Apache Hive](https://hive.apache.org/) que usam o Tez como o mecanismo de execução. A interface do usuário do Tez visualiza o trabalho como um grafo de itens conectados, é possível detalhar cada item e recuperar estatísticas e informações de registro.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster HDInsight baseado no Windows. Para obter as etapas de criação de um novo cluster, confira [Introdução ao uso do HDInsight baseado no Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > A interface do usuário do Apache Tez está disponível apenas em clusters HDInsight baseados no Windows criados após 8 de fevereiro de 2016.
  >
  >
* Um cliente da Área de Trabalho Remota baseado no Windows.

## <a name="understanding-apache-tez"></a>Noções básicas sobre o Apache Tez
O Tez é uma estrutura extensível para processamento de dados no Apache Hadoop e fornece maiores velocidades do que o processamento tradicional do [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html). É possível habilitar o Tez incluindo o texto a seguir como parte de uma consulta do Hive:

    set hive.execution.engine=tez;

O Tez cria um DAG (Grafo Direcionado Acíclico) que descreve a ordem de execução das ações exigidas pelo trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

### <a name="understanding-the-tez-ui"></a>Noções básicas sobre a interface de usuário do Tez
A interface do usuário do Tez é uma página da Web que fornece informações sobre processos que usam o Tez. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.
* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

## <a name="generate-a-dag"></a>Gerar um DAG
A interface do usuário do Tez conterá dados, se um trabalho que usa o mecanismo Tez estiver atualmente em execução ou se foi executado anteriormente. Consultas do Hive simples geralmente podem ser resolvidas sem o uso do Tez. Consultas mais complexas que fazem filtragem, agrupamento, ordenação, junções e etc. exigem o Tez.

Use as etapas a seguir para executar uma consulta do Hive que usa o Tez.

1. Em um navegador da Web, navegue para https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster HDInsight.
2. No menu, na parte superior da página, escolha o **Editor do Hive**. Isso exibe uma página com a consulta de exemplo a seguir.

        Select * from hivesampletable

    Apague o exemplo de consulta e substitua-o pelo que se segue.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Selecione o botão **Enviar**. A **Sessão de Trabalho** na parte inferior da página exibe o status da consulta. Depois que o status mudar para **Concluído**, selecione o link **Exibir Detalhes** para exibir os resultados. A **Saída do Trabalho** deve ser semelhante a esta:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Usar a interface de usuário do Tez
> [!NOTE]
> A interface de usuário do Tez está disponível na área de trabalho dos nós de cabeçalho do cluster, de modo que é preciso usar a Área de Trabalho Remota para se conectar a esses nós de cabeçalho.
>
>

1. No [Portal do Azure](https://portal.azure.com), escolha o cluster HDInsight. Na parte superior da folha HDInsight, selecione o ícone **Área de Trabalho Remota**. Este link exibe a folha da área de trabalho remota

    ![Ícone da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Na folha Área de Trabalho Remota, selecione **Conectar** para se conectar ao nó de cabeçalho do cluster. Quando solicitado, use o nome do usuário e a senha da Área de Trabalho Remota do cluster para autenticar a conexão.

    ![Ícone de conexão da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Se você não tiver habilitado a conectividade da Área de Trabalho Remota, forneça um nome de usuário, uma senha e a data de vencimento, então selecione **Habilitar** para habilitar a Área de Trabalho Remota. Depois da habilitação, use as etapas anteriores para se conectar.
   >
   >
3. Depois de conectado, abra o Internet Explorer na área de trabalho remota, selecione o ícone de engrenagem no canto superior direito do navegador e selecione **Configurações do Modo de Exibição de Compatibilidade**.
4. Na parte inferior de **Configurações do Modo de Exibição de Compatibilidade**, desmarque a caixa de seleção de **Exibir sites da intranet no Modo de Exibição de Compatibilidade** e **Usar listas de compatibilidade da Microsoft** e selecione **Fechar**.
5. No Internet Explorer, navegue até http://headnodehost:8188/tezui/#/. Isso exibe a interface do usuário do Tez

    ![Interface de usuário do Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Quando a interface do usuário do Tez é carregada, você vê uma lista de DAGs atualmente em execução ou as que foram executados no cluster. A visualização padrão inclui o nome do DAG, ID, Remetente, Status, Hora de Início, Hora de Término, Duração, ID do Aplicativo e Fila. É possível adicionar mais colunas usando o ícone de engrenagem no canto direito da página.

    Se você tiver apenas uma entrada, ela representará a consulta que você executou na seção anterior. Se você tiver várias entradas, será possível pesquisar ao inserir critérios de pesquisa nos campos acima dos DAGs e pressionar **Enter**.
6. Escolha o **Nome do Dag** para a entrada mais recente do DAG. Este link exibe informações sobre o DAG, além da opção de baixar um zip de arquivos JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Acima dos **Detalhes do DAG**, há vários links que podem ser usados para exibir informações sobre o DAG.

   * **Contadores de DAG** exibe informações de contadores do DAG em questão.
   * **Modo de Exibição Gráfico** exibe uma representação gráfica deste DAG.
   * **Todos os Vértices** exibe uma lista dos vértices neste DAG.
   * **Todas as Tarefas** exibe uma lista das tarefas de todos os vértices neste DAG.
   * **Todas as Tentativas de Tarefa** exibe informações sobre as tentativas de execução de tarefas para este DAG.

     > [!NOTE]
     > Se você rolar a exibição da coluna até Vértices, Tarefas e Tentativas de Tarefa, observe que há links para exibir **contadores** e **exibir ou baixar logs** para cada linha.
     >
     >

     Se houver uma falha com o trabalho, os Detalhes do DAG exibirão um status de FALHA, juntamente com links para informações sobre a tarefa com falha. As informações de diagnóstico são exibidas abaixo dos detalhes do DAG.
8. Escolha **Modo de Exibição Gráfico**. Isso exibe uma representação gráfica do DAG. Você pode colocar o mouse sobre cada vértice no modo de exibição para exibir informações sobre ele.

    ![Modo de Exibição Gráfico](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Clicar em um vértice carrega os **Detalhes do Vértice** para aquele item. Clique no vértice **Mapa 1** para exibir detalhes do item em questão. Selecione **Confirmar** para confirmar a navegação.

    ![Detalhes do Vértice](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Observe que agora você tem links relacionados às tarefas e aos vértices na parte superior da página.

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
11. Escolha **Tarefas** e selecione o item chamado **00_000000**. Este link exibe os **Detalhes da Tarefa** dessa tarefa. Nessa tela, você pode exibir **Contadores de Tarefa** e **Tentativas de Tarefa**.

    ![Detalhes de tarefa](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como usar a visualização do [Apache TEZ](https://tez.apache.org/), saiba mais sobre [Usando o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, consulte a página do [Apache Tez em Hortonworks](https://hortonworks.com/hadoop/tez/).
