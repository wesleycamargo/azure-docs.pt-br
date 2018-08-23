---
title: Conectar-se ao Azure HDInsight usando as Ferramentas do Data Lake para Visual Studio
description: Saiba como instalar e usar as Ferramentas do Data Lake para Visual Studio para se conectar a clusters Hadoop no Azure HDInsight e executar consultas do Hive.
keywords: ferramentas do hadoop, consulta hive, visual studio, hadoop do visual studio
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive, hdiseo17may2017
ms.topic: get-started-article
ms.date: 05/16/2018
ms.openlocfilehash: d4f444133e8fe6f5ba1a8c12ce08d5c66e8fc18e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "41920954"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Usar Ferramentas do Data Lake para Visual Studio a fim de se conectar ao Azure HDInsight e executar consultas Hive

Saiba como usar as Ferramentas do Data Lake para Visual Studio (também chamadas Ferramentas do Azure Data Lake e Stream Analytics para Visual Studio) a fim de se conectar a clusters Hadoop no [Azure HDInsight](../hdinsight-hadoop-introduction.md) e enviar consultas Hive. 

Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight](../hdinsight-hadoop-introduction.md) e [Começar a usar o HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Para obter mais informações sobre como se conectar ao cluster Storm, consulte [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

As Ferramentas do Data Lake para Visual Studio podem ser usadas para acessar o Data Lake Analytics e o HDInsight. Para obter informações sobre as Ferramentas do Data Lake, confira [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial e usar as Ferramentas do Data Lake para Visual Studio, você precisa dos seguintes itens:

* Um cluster Azure HDInsight. Para criar um cluster Azure HDInsight, confira [Introdução ao uso do Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas do Hive interativas, você precisa de um cluster de [Consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* Um computador que tenha o Visual Studio 2017, 2015 ou 2013 instalado.
    
    > [!NOTE]
    > Atualmente, somente a versão em inglês das Ferramentas do Data Lake para Visual Studio está disponível.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Instalar ou atualizar as Ferramentas do Azure Data Lake para Visual Studio

### <a name="install-data-lake-tools"></a>Instalar as Ferramentas do Data Lake

As Ferramentas do Data Lake estão instaladas por padrão no Visual Studio 2017. Para versões anteriores do Visual Studio, você pode instalar as Ferramentas do Data Lake usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx). Escolha a versão das Ferramentas do Data Lake que correspondem à versão do Visual Studio. 

### <a name="install-visual-studio"></a>Instalar Visual Studio

Se não tiver instalado o Visual Studio, use o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) para instalar as versões mais recentes do Visual Studio Community e o Azure SDK:

![Captura de tela do Web Platforms Installer das Ferramentas do Data Lake para Visual Studio.](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Usar o Web Platform Installer para instalar as Ferramentas do Data Lake para Visual Studio")

### <a name="update-the-tools"></a>Atualizar as ferramentas

1. Abra o Visual Studio.
2. No menu **Ferramentas**, selecione **Extensões e atualizações**.
3. Expanda **Atualizações**e selecione **Ferramentas do Azure Data Lake e Stream Analytics** (se estiverem instaladas).

> [!NOTE]
>
> Você pode usar somente as Ferramentas do Data Lake versão 2.3.0.0 ou posterior para se conectar aos clusters de Consulta Interativa e executar consultas interativas Hive.

## <a name="connect-to-azure-subscriptions"></a>Conectar-se a assinaturas do Azure
Você pode usar as ferramentas do Data Lake para Visual Studio para se conectar a clusters HDInsight, executar algumas operações básicas de gerenciamento e executar consultas Hive.

> [!NOTE]
> Para obter informações sobre como se conectar a um cluster Hadoop genérico, confira [Escrever e enviar consultas Hive usando o Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).
> 
> 

Para conectar-se à sua assinatura do Azure:

1. Abra o Visual Studio.
2. No menu **Visualizar**, selecione **Gerenciador de Servidores**.
3. No Gerenciador de Servidores, expanda **Azure** e expanda **HDInsight**.
   
   > [!NOTE]
   > A janela **Lista de Tarefas do HDInsight** deve estar aberta. Se a janela não for exibida, no menu **Exibição**, selecione **Outras Janelas**e selecione **Janela de Lista de Tarefas do HDInsight**.  
   > 
   > 
4. Insira as credenciais de assinatura do Azure e selecione **Entrar**. A autenticação só será necessária se você nunca se conectou à assinatura do Azure pelo Visual Studio nesse computador.
5. No Gerenciador de Servidores aparece uma lista de clusters existentes do HDInsight. Se não tiver nenhum cluster, você poderá criar um usando o portal do Azure, o Azure PowerShell ou o SDK do HDInsight. Para saber mais, veja [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Captura de tela da lista de clusters das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Lista de clusters das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores")
6. Expanda um cluster do HDInsight. O **Bancos de Dados do Hive**, uma conta de armazenamento padrão, contas de armazenamento vinculadas e o **Log de Serviço do Hadoop** aparecem. Você pode expandir mais as entidades.

Depois de se conectar à assinatura do Azure, você poderá fazer as tarefas a seguir.

Conectar-se ao portal do Azure pelo Visual Studio:

1. Em Gerenciador de Servidores, selecione **Azure** > **HDInsight**.
2. Clique com o botão direito do mouse no cluster HDInsight e selecione **Gerenciar Cluster no portal do Azure**.

Para fazer perguntas e comentários sobre o Visual Studio:

1. No menu **Ferramentas**, selecione **HDInsight**.
2. Para fazer perguntas, selecione **Fórum do MSDN**. Para fornecer comentários, selecione **Fornecer Comentários**.

## <a name="explore-linked-resources"></a>Explorar recursos vinculados
No Gerenciador de Servidores, você pode visualizar a conta de armazenamento padrão e as contas de armazenamento vinculadas. Se você expandir a conta de armazenamento padrão, visualizará os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Clique com o botão direito do mouse em qualquer um dos contêineres para ver o conteúdo do contêiner.

![Captura de tela da lista de recursos vinculados das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Listar recursos vinculados")

Depois de abrir um contêiner, você poderá usar os botões abaixo para carregar, excluir e baixar blobs:

![Captura de tela das operações de blobs das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Carregar, excluir e baixar blobs no Gerenciador de Servidores")

## <a name="run-interactive-hive-queries"></a>Executar consultas interativas do Hive
O [Apache Hive](http://hive.apache.org) é uma infraestrutura do data warehouse criada no Hadoop. O Hive é usado para análise, consultas e resumo de dados. Você pode usar as Ferramentas do Data Lake para Visual Studio a fim de executar consultas Hive pelo Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight](hdinsight-use-hive.md).

A [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md) usa [Hive em LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) no Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas de estilo data warehouse complexas em conjuntos de dados grandes e armazenados. A execução de consultas Hive na Consulta Interativa é muito mais rápida em comparação com os trabalhos em lotes tradicionais do Hive. Para saber mais, confira [executar trabalhos em Lotes do Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> Você só pode executar consultas interativas do Hive quando se conecta a um cluster de [Consulta Interativa HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Use também as Ferramentas do Data Lake para Visual Studio a fim de ver o que está em um trabalho do Hive. As Ferramentas do Data Lake para Visual Studio coletam e revelam logs Yarn de determinados trabalhos do Hive.

### <a name="view-hivesampletable"></a>Exibir **hivesampletable**
Todos os clusters HDInsight vêm com uma tabela Hive de exemplo chamada hivesampletable. A tabela do Hive define como listar tabelas do Hive, exibir os esquemas de tabela e listar as linhas da tabela do Hive.

Listar tabelas do Hive e exibir o esquema da tabela do Hive:

1. Para ver o esquema da tabela, em **Gerenciador de Servidores**, selecione **Azure** > **HDInsight**. Selecione o cluster e selecione **Bancos de Dados do Hive** > **Padrão** > **hivesampletable**.
2. Clique com o botão direito do mouse em **hivesampletable** e clique em **Exibir as 100 primeiras linhas** para listá-las. Isso é o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   É possível personalizar a contagem de linhas.
   
   ![Captura de tela de uma consulta de esquema do Hive do HDInsight para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Resultados da consulta Hive")

### <a name="create-hive-tables"></a>Criar tabelas Hive
Você pode usar a GUI ou usar consultas Hive para criar uma tabela Hive. Para obter informações sobre como usar consultas de Hive, consulte [Executar consultas Hive](#run.queries).

Para criar uma tabela Hive:

1. Em **Gerenciador de Servidores**, selecione **Azure** > **Clusters HDInsight**. Selecione seu cluster HDInsight e selecione **Bancos de Dados do Hive**.
2. Clique com botão direito do mouse em **Padrão**e selecione **Criar Tabela**.
3. Configure a tabela.  
4. Selecione **Criar Tabela** para enviar o trabalho e criar uma nova tabela Hive.
   
    ![Captura de tela da janela Criar Tabela das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Criar tabela do Hive")

### <a name="run.queries"></a>Validar e executar consultas do Hive
Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas locais
* Criar um aplicativo Hive

Para criar, validar e executar consultas ad hoc:

1. Em **Gerenciador de Servidores**, selecione **Azure** > **Clusters HDInsight**.
2. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e selecione **Escrever uma consulta Hive**.  
3. Digite as consultas Hive. 

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se você digitar **SELECT * FROM**, o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos.
   
    ![Captura de tela de um exemplo 1 de IntelliSense das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![Captura de tela de um exemplo 2 de IntelliSense das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.
   > 
   
4. (Opcional) Para verificar os erros de sintaxe do script, selecione **Validar Script**.
   
    ![Captura de tela da validação local das Ferramentas do Data Lake para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Validar script")
5. Selecione **Enviar** ou **Enviar (Avançado)**. Se você selecionar a opção de envio avançado, configure **Nome do Trabalho**, **Argumentos**, **Configurações Adicionais** e **Diretório de Status** para o script:
   
    ![Captura de tela de uma consulta Hive do HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Enviar consultas")
   
    Depois de enviar o trabalho, a janela **Resumo de Trabalhos do Hive** aparece.
   
    ![Captura de tela de um resumo de uma consulta Hive do HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Resumo de trabalhos do Hive")
6. Use o botão **Atualizar** para atualizar o status até que o status do trabalho seja alterado para **Concluído**.
7. Selecione os links na parte inferior para ver: **Consulta de Trabalho**, **Saída de Trabalho**, **Log do Trabalho** ou **Log do Yarn**.

Para criar e executar uma solução de Hive:

1. No menu **Arquivo**, selecione **Novo** e **Projeto**.
2. No painel esquerdo, escolha **HDInsight**. No painel central, selecione **Aplicativo Hive**. Insira as propriedades e selecione **OK**.
   
    ![Captura de tela de um novo projeto Hive das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Criar aplicativos Hive pelo Visual Studio")
3. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.
4. Para validar o script do Hive, selecione o botão **Validar Script**. Alternativamente, clique com o botão direito do mouse no script no editor de Hive e selecione **Validar Script** no menu de contexto.

### <a name="view-hive-jobs"></a>Exibir trabalhos Hive
Você pode exibir consultas de trabalho, saída do trabalho, logs de trabalho e logs do Yarn para trabalhos do Hive. Para saber mais, confira a captura de tela anterior.

Na versão mais recente das ferramentas, você pode ver o que está dentro de trabalhos do Hive coletando e identificando logs do Yarn. Um log do Yarn pode ajudar você a investigar problemas de desempenho. Para saber mais sobre como o HDInsight coleta logs do Yarn, confira [Acessar logs de aplicativo do HDInsight programaticamente](../hdinsight-hadoop-access-yarn-app-logs.md).

Para exibir trabalhos do Hive:

1. No **Gerenciador de Servidores**, expanda **Azure** e expanda **HDInsight**.
2. Clique com o botão direito do mouse em um cluster HDInsight e selecione **Exibir Trabalhos**. Uma lista dos trabalhos do Hive executados no cluster aparecerá.  
3. Selecione um trabalho. Na janela **Resumo do Trabalho de Hive**, selecione uma das seguintes opções:
    - **Consulta do Trabalho**
    - **Saída do trabalho**
    - **Log de trabalhos**  
    - **Log do Yarn**
   
    ![Captura de tela da janela Exibir trabalhos do Hive das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Exibir trabalhos do Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Execução do Hive de caminho mais rápido via HiveServer2
> [!NOTE]
> Esse recurso funciona apenas em um cluster HDInsight versão 3.2 ou posterior.
 
As ferramentas do Data Lake para Visual Studio usadas para enviar trabalhos Hive via [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (também conhecido como Templeton). Esse método para enviar trabalhos do Hive levou muito tempo para retornar informações sobre erros e detalhes do trabalho.

Para resolver esse problema de desempenho, as Ferramentas do Data Lake para Visual Studio podem ignorar o RDP/SSH e executar trabalhos do Hive diretamente no cluster por meio do HiveServer2.

Além de melhorar o desempenho, com esse método você também pode exibir o Hive nos gráficos do Apache Tez e nos detalhes da tarefa.

Em um cluster HDInsight versão 3.2 ou posterior, um botão **Executar via HiveServer2** é exibido:

![Captura de tela da opção Executar via HiveServer2 das Ferramentas do Data Lake para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Executar consultas Hive usando o HiveServer2")

Você também pode ver o streaming dos logs em tempo real. Você também pode ver os gráficos de trabalho se a consulta de Hive é executada no Tez.

![Captura de tela da execução do Hive mais rápida com o HiveServer 2 nas ferramentas do Data Lake para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Exibir gráficos de trabalho")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Executar consultas via HiveServer2 comparado com enviar consultas via WebHCat

Embora você tenha muitos benefícios de desempenho ao executar consultas via HiveServer2, esse método tem várias limitações. Algumas das limitações tornam esse método inadequado para uso em produção. 

A tabela abaixo mostra as diferenças entre executar consultas via HiveServer2 e enviar consultas via WebHCat:

|  | Executar via HiveServer2 | Enviar via WebHCat |
| --- | --- | --- |
| Executar consultas |Elimina a sobrecarga no WebHCat (que inicia um Trabalho MapReduce chamado TempletonControllerJob). |Se uma consulta é executada por meio do WebHCat, o WebHCat inicia um trabalho MapReduce que introduz latência adicional. |
| Transmitir os logs |Quase em tempo real. |Os logs de execução de trabalho só estarão disponíveis quando o trabalho for concluído. |
| Exibir histórico de trabalho |Se uma consulta é executada por meio do HiveServer2, seu histórico de trabalho (log de trabalho, saída do trabalho) não é preservado. Você pode exibir o aplicativo na interface de usuário do Yarn com informações limitadas. |Se uma consulta é executada por meio do WebHCat, seu histórico de trabalho (log de trabalho, saída do trabalho) não é preservado. Você pode exibir o histórico de trabalho usando o Visual Studio, o SDK do HDInsight ou o PowerShell. |
| Fechar janela |A execução via HiveServer2 é *síncrona*. Se as janelas estiverem fechadas, a execução da consulta será cancelada. |O envio via WebHCat é *assíncrono*. Você pode enviar a consulta via WebHCat e fechar o Visual Studio. Você pode voltar e ver os resultados a qualquer momento. |

### <a name="tez-hive-job-performance-graph"></a>Grafo de desempenho do trabalho do Tez Hive
Nas Ferramentas do Data Lake para Visual Studio, você pode ver os gráficos de desempenho para os trabalhos do Hive que o mecanismo de execução Tez executa. Para saber mais sobre como habilitar o Tez, consulte [Usar o Hive no HDInsight](hdinsight-use-hive.md). 

Depois de enviar um trabalho do Hive no Visual Studio, o Visual Studio exibirá o gráfico quando o trabalho for concluído. Talvez seja necessário selecionar o botão **Atualizar** para exibir o status mais recente do trabalho.

> [!NOTE]
> Este recurso está disponível apenas para um cluster HDInsight versão 3.2.4.593 ou posterior. O recurso só funciona em trabalhos concluídos. Você também deve enviar trabalho via WebHCat para usar esse recurso. A imagem a seguir é exibida quando você executa a consulta via HiveServer2: 
> 
> ![Captura de tela de um gráfico de desempenho do Hive Tez do Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Status do trabalho")

Para ajudá-lo a entender melhor a consulta do Hive, um modo de exibição do operador de Hive foi adicionado a esta versão. Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico do trabalho. Você também pode apontar para um operador específico e ver mais detalhes sobre o operador.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Exibição de Execução de Tarefa para o Hive em trabalhos do Tez
Você pode usar o Modo de Exibição de Execução de Tarefa em trabalhos do Hive Tez para obter informações estruturadas e visualizadas sobre trabalhos de Hive. Você também pode obter mais detalhes sobre o trabalho. Se ocorrerem problemas de desempenho, você poderá usar o modo de exibição para obter mais detalhes sobre o problema. Por exemplo, você pode obter informações sobre como funciona cada tarefa e informações detalhadas sobre cada tarefa (leitura/gravação de dados, agendamento/início/término e assim por diante). Use as informações para ajustar as configurações de trabalho ou a arquitetura do sistema com base nas informações visualizadas.

![Captura de tela da janela de exibição da execução de tarefas das Ferramentas do Visual Studio para o Data Lake ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Modo de Exibição de Execução de Tarefa")

## <a name="run-hive-batch-jobs"></a>Executar trabalhos de lote do Hive
O teste do script do Hive em um cluster HDInsight (com exceção do cluster da Consulta Interativa) pode demorar. O processo pode demorar vários minutos ou mais. As Ferramentas do Data Lake para Visual Studio podem validar o script do Hive localmente sem se conectar a um cluster ativo. Para saber mais sobre como executar consultas interativas, consulte [Executar consultas interativas de Hive](#run-interactive-hive-queries).

Você pode usar as Ferramentas do Data Lake para Visual Studio a fim de ver o que há dentro do trabalho do Hive coletando e identificando os logs do Yarn de trabalhos do Hive específicos.

Para saber mais sobre como executar trabalhos em lotes do Hive, confira [Executar consultas Hive interativas](#run-interactive-hive-queries). As informações nesta seção se aplicam à execução de trabalhos em lotes do Hive com mais tempo de execução.

## <a name="run-pig-scripts"></a>Executar scripts do Pig
Você pode usar as Ferramentas do Data Lake para Visual Studio para criar e enviar scripts Pig para clusters do HDInsight. Primeiro, crie um projeto de Pig usando um modelo. Em seguida, envie o script para clusters HDInsight.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
* Atualmente, os resultados do HiveServer2 são exibidos na forma de texto sem formatação, o que não é ideal. A Microsoft está trabalhando para corrigir esse problema.
* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são mostrados. Se você estiver bloqueado em relação a esse problema, contate a equipe de suporte.
* O script HQL criado pelo Visual Studio é codificado, dependendo da configuração de região local do usuário. O script não executa corretamente se você carrega o script em um cluster como um arquivo binário.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o pacote Ferramentas do Data Lake para Visual Studio para conectar-se aos clusters HDInsight pelo Visual Studio. Você também aprendeu a executar uma consulta do Hive. Para obter mais informações, consulte estes artigos:

* [Usar o Hive do Hadoop no HDInsight](hdinsight-use-hive.md)
* [Introdução ao uso do Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos Hadoop no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter com Hadoop no HDInsight](../hdinsight-analyze-twitter-data.md)

