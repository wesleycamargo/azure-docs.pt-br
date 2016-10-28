<properties
pageTitle="Usar a interface de usuário do Tez com o HDInsight baseado em Windows | Azure"
description="Saiba como usar a interface de usuário do Tez para depurar trabalhos do Tez no HDInsight baseado no Windows."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/19/2016"
ms.author="larryfr"/>

# Usar a interface de usuário do Tez para depurar trabalhos no HDInsight baseado no Windows

A interface de usuário do Tez é uma página da Web que pode ser usada para entender e depurar trabalhos que usam o Tez como o mecanismo de execução em clusters HDInsight baseados no Windows. A interface de usuário do Tez permite que você visualize o trabalho como um gráfico de itens conectados, detalhe cada item e recupere estatísticas e informações de registro.

> [AZURE.NOTE] As informações neste documento são específicas de clusters HDInsight baseados em Windows. Para saber mais sobre como exibir e depurar o Tez no HDInsight baseado em Linux, confira [Use Ambari Views to debug Tez jobs on HDInsight](hdinsight-debug-ambari-tez-view.md).

##Pré-requisitos

* Um cluster HDInsight baseado no Windows. Para obter as etapas de criação de um novo cluster, confira [Introdução ao uso do HDInsight baseado no Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

    > [AZURE.IMPORTANT] A interface de usuário do Tez está disponível apenas em clusters HDInsight baseados no Windows criados depois de 8 de fevereiro de 2016.

* Um cliente da Área de Trabalho Remota baseado no Windows.

##Noções básicas sobre o Tez

Tez é uma estrutura extensível para processamento de dados no Hadoop que fornece maior velocidade de processamento do que o MapReduce tradicional. Para os clusters HDInsight baseados no Windows, é um mecanismo opcional que você pode habilitar para o Hive usando o comando a seguir como parte de sua consulta do Hive:

    set hive.execution.engine=tez;

Quando o trabalho é enviado ao Tez, ele cria um DAG (Gráfico Acíclico Dirigido) que descreve a ordem de execução das ações exigidas pelo trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

###Noções básicas sobre a interface de usuário do Tez

A interface de usuário do Tez é uma página da Web que fornece informações sobre processos em execução ou que foram executados anteriormente com o Tez. Ela permite exibir o DAG gerado pelo Tez, como ele é distribuído entre os clusters, contadores, como a memória usada por tarefas e vértices, além de informações de erro. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.

* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

##Gerar um DAG

A interface de usuário do Tez conterá apenas dados se um trabalho que usa o mecanismo Tez estiver sendo executado no momento ou se tiver sido executado anteriormente. Normalmente, as consultas simples do Hive podem ser resolvidas sem usar o Tez. Porém, as consultas mais complexas que realizam filtragem, agrupamento, ordenação, associações etc. normalmente exigirão o Tez.

Use as etapas a seguir para executar uma consulta do Hive usando o Tez.

1. Em um navegador da Web, navegue até https://CLUSTERNAME.azurehdinsight.net, em que __NOMEDOCLUSTER__ é o nome do seu cluster HDInsight.

2. No menu, na parte superior da página, escolha o __Editor do Hive__. Essa ação exibirá uma página com o exemplo de consulta a seguir.

        Select * from hivesampletable

    Apague o exemplo de consulta e substitua-o pelo que se segue.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. Selecione o botão __Enviar__. A seção __Sessão de Trabalho__, na parte inferior da página, exibirá o status da consulta. Depois que o status mudar para __Concluído__, selecione o link __Exibir Detalhes__ para exibir os resultados. A __Saída do Trabalho__ deve ser semelhante a esta:
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##Usar a interface de usuário do Tez

> [AZURE.NOTE] A interface de usuário do Tez está disponível na área de trabalho dos nós de cabeçalho do cluster, de modo que é preciso usar a Área de Trabalho Remota para se conectar a esses nós de cabeçalho.

1. No [Portal do Azure](https://portal.azure.com), escolha o cluster HDInsight. Na parte superior da folha HDInsight, selecione o ícone __Área de Trabalho Remota__. Isso exibirá a folha da área de trabalho remota

    ![Ícone da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. Na folha Área de Trabalho Remota, selecione __Conectar__ para se conectar ao nó de cabeçalho do cluster. Quando solicitado, use o nome do usuário e a senha da Área de Trabalho Remota do cluster para autenticar a conexão.

    ![Ícone de conexão da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] Se você não tiver habilitado a conectividade da Área de Trabalho Remota, forneça um nome de usuário, uma senha e a data de vencimento, então selecione __Habilitar__ para habilitar a Área de Trabalho Remota. Depois da habilitação, use as etapas anteriores para se conectar.

3. Depois de conectado, abra o Internet Explorer na área de trabalho remota, selecione o ícone de engrenagem no canto superior direito do navegador e selecione __Configurações do Modo de Exibição de Compatibilidade__.

4. Na parte inferior de __Configurações do Modo de Exibição de Compatibilidade__, desmarque a caixa de seleção de __Exibir sites da intranet no Modo de Exibição de Compatibilidade__ e __Usar listas de compatibilidade da Microsoft__ e selecione __Fechar__.

5. No Internet Explorer e navegue até http://headnodehost:8188/tezui/#/. Isso exibirá a interface de usuário do Tez

    ![Interface de usuário do Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Quando a interface de usuário do Tez for carregada, você verá uma lista de DAGs atualmente em execução ou que foram executados no cluster. O modo de exibição padrão inclui Nome, Id, Remetente, Status, Hora de Início, Hora de Término, Duração, ID do Aplicativo e Fila do Dag. É possível adicionar mais colunas usando o ícone de engrenagem no canto direito da página.

    Se você tiver apenas uma entrada, ela será para a consulta que você executou na seção anterior. Se você tiver várias entradas, será possível pesquisar ao inserir critérios de pesquisa nos campos acima dos DAGs e pressionar __Enter__.

4. Escolha o __Nome do Dag__ para a entrada mais recente do DAG. Isso exibirá informações sobre o DAG, bem como a opção para baixar um zip de arquivos JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. Acima dos __Detalhes do DAG__, há vários links que podem ser usados para exibir informações sobre o DAG.

    * __Contadores de DAG__ exibe informações de contadores do DAG em questão.
    
    * __Modo de Exibição Gráfico__ exibe uma representação gráfica deste DAG.
    
    * __Todos os Vértices__ exibe uma lista dos vértices neste DAG.
    
    * __Todas as Tarefas__ exibe uma lista das tarefas de todos os vértices neste DAG.
    
    * __Todas as Tentativas de Tarefa__ exibe informações sobre as tentativas de execução de tarefas para este DAG.
    
    > [AZURE.NOTE] Se você rolar a exibição da coluna até Vértices, Tarefas e Tentativas de Tarefa, observe que há links para exibir __contadores__ e __exibir ou baixar logs__ para cada linha.

    Se houver uma falha com o trabalho, os Detalhes do DAG exibirão um status de FALHA, juntamente com links para informações sobre a tarefa com falha. As informações de diagnóstico serão exibidas abaixo dos detalhes do DAG.

7. Escolha __Modo de Exibição Gráfico__. Isso exibe uma representação gráfica do DAG. Você pode colocar o mouse sobre cada vértice no modo de exibição para exibir informações sobre ele.

    ![Modo de exibição Gráfico](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. Clicar em um vértice carregará os __Detalhes do Vértice__ para esse item. Clique no vértice __Mapa 1__ para exibir detalhes do item em questão. Selecione __Confirmar__ para confirmar a navegação.

    ![Detalhes do vértice](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. Observe que agora você tem links relacionados às tarefas e aos vértices na parte superior da página.

    > [AZURE.NOTE] Você também pode chegar a essa página voltando para __Detalhes do DAG__, selecionando __Detalhes do Vértice__ e selecionando o vértice __Mapa 1__.

    * __Contadores de Vértice__ exibe informações de contadores do vértice em questão.
    
    * __Tarefas__ exibe tarefas do vértice em questão.
    
    * __Tentativas de Tarefa__ exibe informações sobre as tentativas de execução de tarefas do vértice em questão.
    
    * __Fontes e Coletores__ exibe fontes de dados e coletores do vértice em questão.

    > [AZURE.NOTE] Assim como no menu anterior, você pode rolar a exibição da coluna para Tarefas, Tentativas de Tarefa e Fontes de Coletores para exibir links para outras informações sobre cada item.

10. Escolha __Tarefas__ e selecione o item chamado __00_000000_\_. Isso exibirá os __Detalhes da Tarefa__ desta tarefa. Nessa tela, você pode exibir __Contadores de Tarefa__ e __Tentativas de Tarefa\_\_.

    ![Detalhes de tarefa](./media/hdinsight-debug-tez-ui/taskdetails.png)

##Próximas etapas

Agora que você aprendeu a usar o modo de exibição do Tez, saiba mais sobre [Como usar o Hive no HDInsight](hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, confira a [página sobre o Tez em Hortonworks](http://hortonworks.com/hadoop/tez/).

<!---HONumber=AcomDC_0914_2016-->