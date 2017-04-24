---
title: Saiba como usar as Ferramentas do Data Lake (HDInsight) para Visual Studio | Microsoft Docs
description: Saiba como instalar e usar as Ferramentas do Data Lake (HDInsight) para Visual Studio para se conectar a um cluster Hadoop e executar uma consulta Hive.
keywords: hadoop tools,hive query,visual studio
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 86641734634df131fd366e0b9e7cc6f0d4e54b7e
ms.lasthandoff: 04/18/2017


---
# <a name="get-started-using-azure-data-lake-hdinsight-tools-for-visual-studio-to-run-a-hive-query"></a>Introdução ao uso das Ferramentas do Azure Data Lake (HDInsight) para Visual Studio para executar uma consulta Hive
Saiba como usar as Ferramentas do Data Lake para Visual Studio para se conectar ao clusters HDInsight e enviar consultas Hive. Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight][hdinsight.introduction] e [Começar a usar o HDInsight][hdinsight.get.started]. Para obter mais informações sobre como se conectar ao cluster Storm, consulte [Develop C# topologies for Apache Storm on HDInsight using Visual Studio][hdinsight.storm.visual.studio.tools] (Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio).

As Ferramentas do Data Lake para Visual Studio podem ser usadas para acessar Data Lake Analytics e HDInsight.  Para obter informações sobre as Ferramentas do Data Lake, confira [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

**Pré-requisitos**

Para concluir este tutorial e usar as ferramentas do Data Lake no Visual Studio, você precisará do seguinte:

* Um cluster Azure HDInsight: para criar um, confira [Começar a usar o HDInsight baseado em Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
* Uma estação de trabalho com o seguinte software:
  
  * Windows 10, Windows 8.1, Windows 8, ou Windows 7.
  * Visual Studio 2013/2015/2017.
    
    > [!NOTE]
    > Atualmente, as ferramentas do Data Lake para Visual Studio são fornecidas apenas na versão em inglês.
    > 
    > 

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio

As Ferramentas do Data Lake estão instaladas por padrão no Visual Studio 2017. Em versões antigas, você pode instalá-las usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/). Você deve escolher aquela que corresponde à sua versão do Visual Studio. Se não tiver instalado o Visual Studio, você poderá instalar a mais recente Visual Studio Community e o SDK do Azure usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/):

![Web Platform installer de Ferramentas do Data Lake para Visual Studio.][1]

## <a name="connect-to-azure-subscriptions"></a>Conectar-se a assinaturas do Azure
As ferramentas do Data Lake para Visual Studio permitem que você se conecte a clusters HDInsight, execute algumas operações básicas de gerenciamento e execute consultas Hive.

> [!NOTE]
> Para obter informações sobre como se conectar a um cluster Hadoop genérico, confira [escrever e enviar consultas Hive usando o Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).
> 
> 

**Para conectar-se à sua assinatura do Azure**

1. Abra o Visual Studio.
2. No menu **Exibir**, clique em **Gerenciador de Servidores** para abrir a janela Gerenciador de Servidores.
3. Expanda **Azure** e expanda **HDInsight**.
   
   > [!NOTE]
   > Observe se a janela **lista de tarefas do HDInsight** está aberta. Se não estiver, clique em **Outras Janelas** no menu **Exibição** e clique na **Janela de Lista de Tarefas do HDInsight** para abri-la.  
   > 
   > 
4. Insira as credenciais de assinatura do Azure e clique em **Entrar**. Isso só será necessário se você nunca se conectou à assinatura do Azure pelo Visual Studio nesta estação de trabalho.
5. No Gerenciador de Servidores, você verá uma lista de clusters existentes do HDInsight. Se não tiver nenhum cluster, você poderá criar um usando o portal do Azure, o Azure PowerShell ou o SDK do HDInsight. Para saber mais, veja [Criar clusters HDInsight][hdinsight-create-clusters].
   
   ![Lista de clusters do Gerenciador de Servidores das ferramentas do Data Lake para Visual Studio][5]
6. Expanda um cluster do HDInsight. Você verá **Bancos de Dados do Hive**, uma conta de armazenamento padrão, contas de armazenamento vinculadas e o **Log de Serviço do Hadoop**. Você pode expandir mais as entidades.

Depois de se conectar à assinatura do Azure, você poderá fazer o seguinte:

**Conectar-se ao portal do Azure pelo Visual Studio**

* No Gerenciador de Servidores, expanda **Azure** > **HDInsight**, clique com o botão direito do mouse em um cluster do HDInsight e clique em **Gerenciar Cluster no Portal do Azure**.

**Para fazer perguntas e comentários sobre o Visual Studio**

* No menu **Ferramentas**, clique em **HDInsight** e clique em **Fórum do MSDN** para fazer perguntas ou clique em **Fazer Comentários**.

## <a name="navigate-the-linked-resources"></a>Navegue nos recursos vinculados
No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Se você expandir a conta de armazenamento padrão, visualizará os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Também é possível clicar com botão direito do mouse em qualquer um dos contêineres para ver o conteúdo.

![Lista de clusters do gerenciador de servidores das ferramentas do Data Lake para Visual Studio][2]

Depois de abrir um contêiner, você poderá usar os botões abaixo para carregar, excluir e baixar blobs:

![Ferramentas do Data Lake para operações de blob do gerenciador de servidores do Visual Studio](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png)

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive
O [Apache Hive][apache.hive] é uma infraestrutura de armazenamento de dados criado com base no Hadoop para fornecer resumo de dados, consultas e análises. As ferramentas do Data Lake para o Visual Studio dão suporte às consultas Hive em execução do Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight.hive].

Leva tempo testar o script do Hive em um cluster HDInsight. Pode levar vários minutos ou mais. As Ferramentas do Data Lake para Visual Studio podem validar o script do Hive localmente sem se conectar a um cluster ativo.

As Ferramentas do Data Lake para Visual Studio também permitem que os usuários vejam o que há dentro do trabalho do Hive coletando e identificando os logs do YARN de determinados trabalhos do Hive.

### <a name="view-the-hivesampletable"></a>Exibir a tabela **hivesampletable**
Todos os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Usaremos esta tabela para mostrar como listar tabelas Hive, exibir os esquemas de tabela e listar as linhas da tabela Hive.

**Listar tabelas Hive e exibir o esquema da tabela Hive**

1. Em **Gerenciador de Servidores**, expanda **Azure** > **HDInsight** > o cluster de sua escolha > **Bancos de dados do Hive** > **Padrão** > **hivesampletable** para ver o esquema da tabela.
2. Clique com o botão direito do mouse em **hivesampletable** e clique em **Exibir as 100 primeiras linhas** para listá-las. É o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:
   
     SELECT * FROM hivesampletable LIMIT 100
   
   É possível personalizar a contagem de linhas.
   
   ![Ferramentas do Data Lake: consulta de esquema do Hive do HDInsight para Visual Studio][6]

### <a name="create-hive-tables"></a>Criar tabelas Hive
É possível usar a GUI para criar uma tabela Hive ou usar consultas Hive. Para obter informações sobre como usar consultas de Hive, consulte [Executar consultas Hive](#run.queries).

**Para criar uma tabela Hive**

1. No **Gerenciador de Servidores**, expanda **Azure** > **Clusters HDInsight**, um cluster do HDInsight > **Bancos de dados Hive**, clique com o botão direito do mouse em **Padrão** e clique em **Criar Tabela**.
2. Configure a tabela.
3. Clique em **Criar Tabela** para enviar o trabalho para a criação de uma nova tabela Hive.
   
    ![Ferramentas do Data Lake: criar tabela hive das Ferramentas do HDInsight para Visual Studio][7]

### <a name="run.queries"></a>Validar e executar consultas do Hive
Há duas maneiras de criar e executar consultas Hive:

* Criar consultas locais
* Criar um aplicativo Hive

**Para criar, validar e executar consultas locais**

1. No **Gerenciador de Servidores**, expanda **Azure** e expanda **Clusters HDInsight**.
2. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, clique em **Escrever uma consulta Hive**.
3. Digite as consultas Hive. Observe que o editor Hive dá suporte a IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, quando você digitar "SELECT * FROM", o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, os nomes de coluna serão listados pelo IntelliSense. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos.
   
    ![Ferramentas Data Lake: Ferramentas do Visual Studio IntelliSense para HDInsight][13]
   
    ![Ferramentas Data Lake: Ferramentas do Visual Studio IntelliSense para HDInsight][14]
   
   > [!NOTE]
   > Apenas os metadados dos clusters selecionados na barra de ferramentas de HDInsight serão sugeridos.
   > 
   > 
4. (Opcional): clique em **Validar Script** para verificar os erros de sintaxe do script.
   
    ![Data Lake: Ferramentas do Data Lake para validação de local do Visual Studio][10]
5. Clique em **Enviar** ou **Enviar (Avançado)**. Com a opção de envio avançado, você configurará **Nome do Trabalho**, **Argumentos**, **Configurações Adicionais** e **Diretório de Status** do script:
   
    ![consulta de hive do Hadoop HDInsight][9]
   
    Depois de enviar o trabalho, é possível ver uma janela **Resumo de Trabalhos do Hive** .
   
    ![Resumo de uma consulta de Hive do HDInsight Hadoop][8]
6. Use o botão **Atualizar** para atualizar o status até que o status do trabalho seja alterado para **Concluído**.
7. Clique nos links na parte inferior para ver **Consulta de Trabalho**, **Saída de Trabalho**, **Log do Trabalho** ou **Log do Yarn**.

**Para criar e executar uma solução de Hive**

1. No menu **ARQUIVO**, clique em **Novo** e em **Projeto**.
2. Selecione **HDInsight** no painel esquerdo, selecione **Aplicativo Hive** no painel do meio, insira as propriedades e clique em **OK**.
   
    ![Ferramentas do Data Lake: novo projeto hive das Ferramentas do HDInsight para Visual Studio][11]
3. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.
4. Para validar o script do Hive, você pode clicar no botão **Validar Script** ou clicar com o botão direito do mouse no editor do Hive e clicar em **Validar Script** no menu de contexto.

### <a name="view-hive-jobs"></a>Exibir trabalhos Hive
Você pode exibir consultas de trabalho, saída do trabalho, logs de trabalho e logs do Yarn para trabalhos do Hive. Para obter mais informações, consulte a captura de tela anterior.

A versão mais recente das ferramentas permite que você veja o que está dentro de trabalhos do Hive coletando e identificando logs do YARN. Um log do YARN pode ajudar você investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight coleta logs do YARN, veja [Access HDInsight Application Logs Programmatically][hdinsight.access.application.logs] (Acessar Logs de Aplicativo do HDInsight Programaticamente).

**Para exibir os trabalhos Hive**

1. No **Gerenciador de Servidores**, expanda**Azure** e expanda **HDInsight**.
2. Clique com o botão direito do mouse em um cluster HDInsight e em **Exibir Trabalhos**. Você verá uma lista dos trabalhos do Hive executados no cluster.
3. Clique em um trabalho na lista de trabalhos para selecioná-lo e use a janela **Resumo do Trabalho do Hive** para abrir **Consulta do Trabalho**, **Saída do Trabalho**, **Log do Trabalho** ou **Log do Yarn**.
   
    ![Ferramentas do Data Lake: exibir projetos hive das ferramentas do HDInsight para o Visual Studio][12]

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Execução do Hive de caminho mais rápido via HiveServer2
> [!NOTE]
> Este tutorial funciona com o cluster HDInsight versão 3.2 e mais recentes.
> 
> 

As ferramentas do Data Lake usadas para enviar trabalhos Hive via [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (também conhecido como Templeton). Demorou muito tempo para retornar informações de erro e detalhes do trabalho.
Para resolver esse problema de desempenho, as Ferramentas do Data Lake executam trabalhos do Hive diretamente no cluster por meio do HiveServer2 para que ele ignore RDP/SSH.
Além de melhorar o desempenho, os usuários também podem exibir o Hive em gráficos Tez e os detalhes da Tarefa.

Para o cluster HDInsight versão 3.2 ou posterior, você pode ver um botão **Executar via HiveServer2**:

![Ferramentas do Data Lake para Visual Studio, executar via hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

E você poderá ver os logs transmitidos em tempo real e ver os gráficos de trabalho se a consulta do Hive for executada no Tez.

![Ferramentas do Data Lake para visual studio, execução por caminho rápido de fast](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**Diferença entre executar consultas por meio do HiveServer2 e enviar consultas por meio do WebHCat**

Embora a execução de consultas por meio do HiveServer2 traga muitos benefícios de desempenho, ela tem várias limitações. Algumas das limitações não são adequadas para uso em produção. A tabela a seguir mostra as diferenças:

|  | Execução por meio do HiveServer2 | Envio  por meio do WebHCat |
| --- | --- | --- |
| Executar consultas |Elimina a sobrecarga no WebHCat (que inicia um Trabalho MapReduce chamado "TempletonControllerJob"). |Desde que uma consulta seja executada por meio do WebHCat, o WebHCat iniciará um trabalho MapReduce que introduz latência adicional. |
| Transmitir os logs |Quase em tempo real. |Os logs de execução de trabalho só estarão disponíveis quando o trabalho for concluído. |
| Exibir histórico de trabalho |Se uma consulta for executada por meio do HiveServer2, seu histórico de trabalho dela (log de trabalho, saída do trabalho) não será preservado. O aplicativo pode ser exibido na interface do usuário YARN com informações limitadas. |Se uma consulta for executada por meio do WebHCat, o histórico de trabalho dela (log de trabalho, saída de trabalho) será preservado e poderá ser exibido usando o Visual Studio/SDK do HDInsight/PowerShell. |
| Fechar janela |A execução por meio do HiveServer2 é uma maneira "síncrona" e, assim, você deverá manter as janelas abertas. Se as janelas estiverem fechadas, a execução da consulta será cancelada. |O envio por meio do WebHCat é uma maneira "assíncrona" e, portanto, você poderá enviar a consulta por meio do WebHCat e fechar o Visual Studio. Você pode voltar e ver os resultados a qualquer momento. |

### <a name="tez-hive-job-performance-graph"></a>Gráfico de desempenho do trabalho do Tez Hive
As Ferramentas do Data Lake dão suporte à exibição de gráficos de desempenho para os trabalhos Hive executados pelo mecanismo de execução Tez. Para saber mais sobre como habilitar Tez, consulte usar o [Hive no HDInsight][hdinsight.hive]. Depois de enviar um trabalho do Hive no Visual Studio, o Visual Studio mostrará o gráfico quando o trabalho for concluído.  Talvez seja necessário clicar no botão **Atualizar** para obter o status mais recente do trabalho.

> [!NOTE]
> Esse recurso só está disponível para o cluster HDInsight em versões posteriores a 3.2.4.593 e funciona somente para trabalhos concluídos (Se você enviar seu trabalho por meio do WebHCat. Esse gráfico mostra quando você executa a consulta por meio de HiveServer2). Isso funciona para clusters baseados em Windows e Linux.
> 
> 

![gráfico de desempenho do hive tez do hadoop](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

Para ajudar você a entender melhor a consulta do Hive, as ferramentas adicionan a Exibição de Operador do Hive nesta versão. Basta clicar duas vezes nos vértices do gráfico do trabalho para ver todos os operadores dentro do vértice. Você também pode passar o mouse sobre um operador específico para exibir mais detalhes desse operador.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Exibição de execução de tarefa para o Hive em trabalhos do Tez
A exibição de execução da Tarefa para o Hive em trabalhos do Tez pode ser usada para obter informações estruturadas e visualizadas para trabalhos do Hive e para obter mais detalhes sobre o trabalho. Quando houver problemas de desempenho, você poderá usar o modo de exibição para obter mais detalhes. Por exemplo, como é o funcionamento de cada tarefa e as informações detalhadas sobre cada tarefa (leitura/gravação de dados, hora de agendamento/inicial/final etc.), de forma que você possa ajustar configurações de trabalho ou a arquitetura do sistema com base nas informações visualizadas.

![Ferramentas do Data Lake para Visual Studio, exibir execução de tarefa](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## <a name="run-pig-scripts"></a>Executar scripts do Pig
As Ferramentas do Data Lake para Visual Studio dão suporte à criação e ao envio de scripts Pig para clusters do HDInsight. Os usuários podem criar um projeto Pig por meio de um modelo e então enviar o script para clusters do HDInsight.

## <a name="feedbacks--known-issues"></a>Comentários e problemas conhecidos
* Atualmente, os resultados do HiveServer2 são exibidos na forma de texto simples, o que não é ideal. Estamos trabalhando para corrigir isso.
* Se os resultados forem iniciados com valores NULL, atualmente os resultados não serão mostrados. Podemos corrigir esse problema. Se você está com bloqueio em relação a isso, fique à vontade para enviar um email ou entrar em contato com a equipe de suporte.
* O script HQL criado pelo Visual Studio é codificado dependendo da configuração de região local do usuário. Ele poderá não ser executado corretamente se o usuário carregar o script no cluster como binário.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como se conectar a clusters HDInsight no Visual Studio usando o pacote de Ferramentas do Data Lake (HDInsight) e como executar consultas Hive. Para obter mais informações, consulte:

* [Usar o Hive do Hadoop no HDInsight][hdinsight.hive]
* [Introdução ao uso do Hadoop no HDInsight][hdinsight.get.started]
* [Enviar trabalhos Hadoop no HDInsight][hdinsight.submit.jobs]
* [Analisar dados do Twitter com Hadoop no HDInsight][hdinsight.analyze.twitter.data]

<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-create-clusters]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

