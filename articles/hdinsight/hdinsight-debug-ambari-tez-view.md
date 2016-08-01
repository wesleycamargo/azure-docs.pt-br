<properties
pageTitle="Usar o modo de exibição do Ambari Tez com HDInsight | Azure"
description="Saiba como usar o modo de exibição do Ambari Tez para depurar trabalhos do Tez no HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/19/2016"
ms.author="larryfr"/>

# Usar os modos de exibição do Ambari para depurar trabalhos do Tez no HDInsight

A interface de usuário da Web do Ambari para HDInsight contém um modo de exibição do Tez que pode ser usado para entender e depurar trabalhos que usam Tez como o mecanismo de execução. O modo de exibição do Tez permite que você visualize o trabalho como um gráfico de itens conectados, detalhe cada item e recupere estatísticas e informações de log.

> [AZURE.NOTE] As informações neste documento são específicas de clusters HDInsight baseados em Linux. Para saber mais sobre como depurar trabalhos do Tez usando o HDInsight baseado em Windows, confira [Usar a interface de usuário do Tez para depurar trabalhos do Tez no HDInsight baseado em Windows](hdinsight-debug-tez-ui.md).

##Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para saber as etapas para criação de um novo cluster, confira [Introdução ao HDInsight baseado no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* Um navegador da Web moderno, com suporte a HTML5.

##Noções básicas sobre o Tez

Tez é uma estrutura extensível para processamento de dados no Hadoop que fornece maior velocidade de processamento do que o MapReduce tradicional. Para clusters HDInsight baseados em Linux, é o mecanismo padrão para Hive.

Quando o trabalho é enviado ao Tez, ele cria um DAG (Gráfico Acíclico Dirigido) que descreve a ordem de execução das ações exigidas pelo trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

###Noções básicas do layout da interface de usuário do Tez

O modo de exibição do Tez fornece informações sobre processos em execução, ou que foram executados anteriormente com o Tez. Ele permite exibir o DAG gerado pelo Tez, como ele é distribuído entre os clusters, contadores, como a memória usada por tarefas e vértices, além de informações de erro. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.

* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

##Gerar um DAG

O modo de exibição do Tez só conterá dados se um trabalho que use o mecanismo Tez estiver sendo executado ou se tiver sido executado anteriormente. Normalmente, as consultas simples do Hive podem ser resolvidas sem usar o Tez. Porém, as consultas mais complexas que realizam filtragem, agrupamento, ordenação, associações etc. normalmente exigirão o Tez.

Use as etapas a seguir para executar uma consulta do Hive usando o Tez.

1. Em um navegador da Web, navegue até https://CLUSTERNAME.azurehdinsight.net, em que __NOMEDOCLUSTER__ é o nome do seu cluster HDInsight.

2. No menu na parte superior da página, escolha o ícone __Modos de exibição__. Isso é semelhante uma série de quadrados. No menu suspenso que aparece, escolha __Modo de exibição do Hive__.

    ![Como selecionar o modo de exibição do Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando o modo de exibição de Hive for carregado, cole o seguinte no Editor de Consulta e clique em __executar__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Após a conclusão do trabalho, você verá a saída exibida na seção __Resultados do Processo de Consulta__. Os resultados devem ser semelhantes ao seguinte:
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Selecione a guia __Log__. Você verá informações parecidas com as seguintes:
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Salve o valor de __ID do aplicativo__, pois ele será usado na próxima seção.

##Usar o modo de exibição do Tez

1. No menu na parte superior da página, escolha o ícone __Modos de exibição__. Na lista suspensa exibida, escolha __Modo de exibição do Tez__.

    ![Como selecionar o modo de exibição do Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando o modo de exibição do Tez for carregado, você verá uma lista de DAGs atualmente em execução ou que foram executados no cluster. O modo de exibição padrão inclui Nome, Id, Remetente, Status, Hora de Início, Hora de Término, Duração, ID do Aplicativo e Fila do Dag. É possível adicionar mais colunas usando o ícone de engrenagem no canto direito da página.

    ![Todos os DAGS](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Se você tiver apenas uma entrada, ela será para a consulta que você executou na seção anterior. Se você tiver várias entradas, pesquise ao inserir a ID do aplicativo no campo __ID do Aplicativo__ e pressione enter.

4. Selecione o __Nome do Dag__. Isso exibirá informações sobre o DAG, bem como a opção para baixar um zip de arquivos JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Acima dos __Detalhes do DAG__, há vários links que podem ser usados para exibir informações sobre o DAG.

    * __Contadores de DAG__ exibe informações de contadores do DAG em questão.
    
    * __Modo de Exibição Gráfico__ exibe uma representação gráfica deste DAG.
    
    * __Todos os Vértices__ exibe uma lista dos vértices neste DAG.
    
    * __Todas as Tarefas__ exibe uma lista das tarefas de todos os vértices neste DAG.
    
    * __Todas as Tentativas de Tarefa__ exibe informações sobre as tentativas de execução de tarefas para este DAG.
    
    > [AZURE.NOTE] Se você rolar a exibição da coluna até Vértices, Tarefas e Tentativas de Tarefa, observe que há links para exibir __contadores__ e __exibir ou baixar logs__ para cada linha.

    Se houver uma falha com o trabalho, os Detalhes do DAG exibirão um status de FALHA, juntamente com links para informações sobre a tarefa com falha. As informações de diagnóstico serão exibidas abaixo dos detalhes do DAG.
    
    ![Uma tela de Detalhes do DAG detalhando uma falha](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Escolha __Modo de Exibição Gráfico__. Isso exibe uma representação gráfica do DAG. Você pode colocar o mouse sobre cada vértice no modo de exibição para exibir informações sobre ele.

    ![Modo de exibição Gráfico](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Clicar em um vértice carregará os __Detalhes do Vértice__ para esse item. Clique no vértice __Mapa 1__ para exibir detalhes do item em questão.

    ![Detalhes do vértice](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Observe que agora você tem links relacionados às tarefas e aos vértices na parte superior da página.

    > [AZURE.NOTE] Você também pode chegar a essa página voltando para __Detalhes do DAG__, selecionando __Detalhes do Vértice__ e selecionando o vértice __Mapa 1__.

    * __Contadores de Vértice__ exibe informações de contadores do vértice em questão.
    
    * __Tarefas__ exibe tarefas do vértice em questão.
    
    * __Tentativas de Tarefa__ exibe informações sobre as tentativas de execução de tarefas do vértice em questão.
    
    * __Fontes e Coletores__ exibe fontes de dados e coletores do vértice em questão.

    > [AZURE.NOTE] Assim como no menu anterior, você pode rolar a exibição da coluna para Tarefas, Tentativas de Tarefa e Fontes de Coletores para exibir links para outras informações sobre cada item.

10. Escolha __Tarefas__ e selecione o item chamado __00_000000_\_. Isso exibirá os __Detalhes da Tarefa__ desta tarefa. Nessa tela, você pode exibir __Contadores de Tarefa__ e __Tentativas de Tarefa\_\_.

    ![Detalhes de tarefa](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##Próximas etapas

Agora que você aprendeu a usar o modo de exibição do Tez, saiba mais sobre [Como usar o Hive no HDInsight](hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, confira a [página sobre o Tez em Hortonworks](http://hortonworks.com/hadoop/tez/).

Para saber mais sobre como usar o Ambari com o HDInsight, confira [Gerenciar clusters HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

<!---HONumber=AcomDC_0720_2016-->