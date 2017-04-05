---
title: "Transmissão de dados do Event Hubs com o Apache Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo a passo sobre como enviar dados de uma transmissão para o Hub de Eventos do Azure e receber esses eventos no HDInsight Spark usando um aplicativo de escala"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 91c60e944dd3b72f5bf1137d93ba2ae70537b2f7
ms.lasthandoff: 03/29/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Streaming Spark: Processa eventos de Hubs de Eventos do Azure com o cluster Apache Spark no HDInsight

Neste artigo, você compreende alguns conceitos relacionados à transmissão usando o Apache Spark e, em seguida, cria uma solução de transmissão que envolve as seguintes etapas:

1. Você pode usar um aplicativo autônomo para ingerir mensagens em um Hub de Eventos do Azure.

2. Você recupera as mensagens do Hub de Eventos em tempo real usando um aplicativo em execução no cluster Spark no Azure HDInsight.

3. Você pode rotear os dados para saídas diferentes, assim como o Azure Storage Blob, tabela de Hive ou uma tabela SQL. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Conceitos de streaming do Spark

Para obter uma explicação detalhada de como o streaming é manipulado no Apache Spark, consulte [Visão Geral de Streaming do Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). O HDInsight traz as mesmas funções de streaming para um cluster Spark no Azure.  

## <a name="what-does-this-solution-do"></a>O que essa solução faz?

Neste artigo, para criar uma solução de transmissão, você realiza as seguintes etapas:

1. Crie um Hub de Eventos do Azure que vá receber uma transmissão de eventos.

2. Execute um aplicativo local autônomo que gere eventos e envie-os por push ao Hub de Eventos do Azure. O aplicativo de exemplo que faz isso é publicado em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Execute um aplicativo de transmissão remotamente em um cluster Spark que leia os eventos de fluxo do Hub de Evento do Azure e envia-os para diferentes locais (Blob do Azure, tabela Hive e tabela do banco de dados SQL). 

## <a name="create-azure-event-hub"></a>Criar Hub de Eventos do Azure

1. Faça logon no [Portal do Azure](https://manage.windowsazure.com) e clique em **Novo** na parte superior esquerda da tela.

2. Clique em **Internet das Coisas** e, em seguida, clique em **Hubs de Eventos**.
   
    ![Criar hub de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub9.png)

3. Na folha **Criar um namespace** , insira um nome de namespace. Escolha o tipo de preço (Básico ou Standard). Além disso, escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado. Clique em **Criar** para criar o namespace.
   
    ![Criar hub de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub1.png)

    > [!NOTE]
       > Você deve selecionar o mesmo **Local** do cluster Apache Spark no HDInsight para reduzir latência e custos.
       > 
       > 

4. Na lista de namespaces do Hubs de Eventos, clique no namespace recém-criado.      
   
    
5. Na folha de namespace, clique em **Hubs de Eventos** e, em seguida, clique em **+ Hub de Eventos** para criar um novo Hub de Eventos.
   
    ![Criar hub de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub3.png)

6. Digite um nome para seu Hub de Eventos, defina a contagem de partições para 10 e a retenção de mensagens para 1. Nós não estamos arquivando as mensagens nesta solução, então você pode deixar o restante como padrão e depois clicar em **Criar**.
   
    ![Criar hub de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub5.png)

7. O Hub de Eventos recém-criado é listado na folha Hub de Eventos.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub6.png)

8. Novamente na folha do namespace (e não na folha do Hub de Eventos específico), clique em **Políticas de acesso compartilhado** e clique em **RootManageSharedAccessKey**.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub7.png)

9. Clique no botão de cópia para copiar a cadeia de conexão e a chave primária **RootManageSharedAccessKey** para a área de transferência. Salve-as para usar posteriormente no tutorial.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub8.png)

## <a name="send-messages-to-an-azure-event-hub-using-a-scala-application"></a>Usar um aplicativo Scala para enviar mensagens ao Hub de Eventos do Azure

Nesta seção, você pode usar um aplicativo Scala autônomo local que gera um fluxo de eventos e envia-os para o Hub de Eventos do Azure criado na etapa anterior. Este aplicativo está disponível no GitHub em [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). As etapas aqui supõem que você já tenha bifurcado esse repositório GitHub.

1. Verifique se você tem os itens a seguir instalados no computador em que você está executando esse aplicativo.

    * Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Um Java IDE. Este artigo usa IntelliJ IDEA 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).


2. Abra o aplicativo **EventhubsSampleEventProducer**no IntelliJ IDEA.

3. Compile o projeto. No menu **Build**, clique em **Criar Projeto**. Dependendo da sua configuração do IDEA IntelliJ, o jar de saída é criado em **\classes\artifacts**.

    > [!TIP]
    > Você também pode usar uma opção disponível no IntelliJ IDEA para criar o projeto diretamente de um repositório GitHub. Para entender como usar essa abordagem, siga as instruções na próxima seção para obter orientação. Observe que muitas etapas descritas na próxima seção não serão aplicável ao aplicativo Scala que você cria nessa etapa. Por exemplo:
    > 
    > * Você não precisa atualizar o POM para incluir a versão do Spark. Isso acontece porque não há nenhuma dependência no Spark para criar esse aplicativo
    > * Você não precisa adicionar alguns jars de dependência à biblioteca de projeto. Esses jars não são necessários para esse projeto.
    > 
    > 

## <a name="receive-messages-from-the-event-hub-using-a-streaming-application-running-on-spark-cluster"></a>Receber mensagens do Hub de eventos usando um aplicativo de streaming em execução no cluster Spark

Um aplicativo Scala de exemplo para receber o evento e encaminhá-lo para diferentes destinos está disponível em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Siga as etapas abaixo para atualizar o aplicativo e criar o jar de saída.

1. Inicie o IntelliJ IDEA e, na tela de inicialização, selecione **Fazer Check-out do Controle de Versão** e clique em **Git**.
   
    ![Obter fontes do Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. Na caixa de diálogo **Clonar Repositório**, forneça a URL para o repositório Git do qual clonar, especifique o diretório para o qual clonar e clique em **Clonar**.
   
    ![Clonar de Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. Siga as instruções até que o projeto estar completamente clonado. Pressione **Alt + 1** para abrir a **Exibição do Projeto**. O resultado deve ser parecido com o seguinte:
   
    ![Exibição do Projeto](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. Verifique se o código do aplicativo foi compilado com Java8. Para garantir isso, clique em **Arquivo**, clique em **Estrutura do Projeto** e, na guia **Projeto**, verifique se o nível de Linguagem do projeto foi definido como **8 - Lambdas, anotações de tipo etc**.
   
    ![Estrutura do projeto](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. Abra o **pom.xml** e verifique se que a versão do Spark está correta. No nó `<properties>`, procure o trecho a seguir e verifique a versão do Spark.
   
        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. O aplicativo requer um jar de dependência chamado **jar do driver JDBC**. Isso é necessário para gravar as mensagens recebidas do hub de eventos em um Banco de Dados SQL do Azure. Você pode baixar v 4.1 ou posterior desse arquivo jar [daqui](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Adicione a referência a esse jar na biblioteca do projeto. Execute as seguintes etapas:
     
     1. Na janela IntelliJ IDEA em que o aplicativo abrir, clique em **Arquivo**, clique em **Estrutura do Projeto** e clique em **Bibliotecas**. 
     2. Clique no ícone de adicionar (![adicionar ícone](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), clique em **Java**e, em seguida, navegue até a localização em que você baixou o jar do driver JDBC. Siga os prompts para adicionar o arquivo jar à biblioteca do projeto.
        
         ![adicionar dependências ausentes](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Adicionar jars de dependência ausente")
     3. Clique em **Aplicar**.
7. Crie o arquivo jar de saída. Execute as seguintes etapas:
   
   1. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e, em seguida, clique em **Dos módulos com dependências**.
      
       ![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. Na caixa de diálogo **Criar JAR de Módulos,**clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) em relação à **Classe Principal**.
   3. Na caixa de diálogo **Selecionar Classe Principal**, selecione qualquer uma das classes disponíveis e clique em **OK**.
      
       ![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, clique em **OK**. Isso cria um JAR único com todas as dependências.
      
       ![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. A guia **Layout de Saída** lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**microsoft-spark-streaming-examples compile output**). Selecione os jars para excluir e, em seguida, clique no ícone **Excluir** (![ícone de excluir](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       Certifique-se de que a caixa **Compilar à criação** esteja marcada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Clique em **Aplicar**.
   6. Na guia **Layout de Saída**, à direita na parte inferior da caixa **Elementos Disponíveis**, você tem o jar do JDBC do SQL adicionado anteriormente à biblioteca do projeto. Você deve adicioná-lo à guia **Layout de Saída** . Clique com o botão direito do mouse no arquivo jar e, em seguida, clique em **Extrair para Raiz de Saída**.
      
       ![Extrair o jar de dependência](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       A guia **Layout de Saída** deve ter esta aparência.
      
       ![Guia de saída final](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       Na caixa de diálogo **Estrutura do Projeto**, clique em **Aplicar** e clique em **OK**.    
   7. Na barra de menus, clique em **Compilar** e, em seguida, clique em **Criar Projeto**. Você também pode clicar em **Compilar Artefatos** para criar o jar. O jar de saída é criado em **\classes\artifacts**.
      
       ![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Executar os aplicativos remotamente em um cluster do Spark usando Livy

Usamos o Livy para executar o aplicativo de transmissão remotamente em um cluster do Spark. Para uma discussão detalhada de como usar Livy com cluster HDInsight Spark, consulte [Enviar trabalhos remotamente para um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Antes de começar a executar os trabalhos remotos para transmitir eventos usando Spark, há algumas ações que você deve executar:

1. Inicie o aplicativo autônomo local para gerar eventos e enviar ao hub de eventos. Use o seguinte comando para fazer isso:
   
        java -cp com-microsoft-azure-eventhubs-client-example.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiar o jar de transmissão (**spark-streaming-data-persistence-examples.jar**) para o armazenamento de Blobs do Azure associado ao cluster. Isso torna o jar acessível ao Livy. Você pode usar [**AzCopy**](../storage/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).
3. Instale CURL no computador do qual você está executando a esses aplicativos. Usamos CURL para invocar os pontos de extremidade do Livy para executar os trabalhos remotamente.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Execute os aplicativos para receber os eventos em um Blob de armazenamento do Azure como texto

Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputBlob.txt** são definidos da seguinte maneira:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Vamos entender quais são os parâmetros no arquivo de entrada:

* **arquivo** é o caminho para o arquivo de jar do aplicativo na conta de armazenamento do Azure associada ao cluster.
* **className** é o nome da classe no jar.
* **args** é a lista de argumentos exigidos pela classe
* **numExecutors** é o número de núcleos usados pelo Spark para executar o aplicativo de transmissão. Isso sempre deve ser pelo menos duas vezes o número de partições do hub de eventos.
* **executorMemory**, **executorCores**, **driverMemory** são parâmetros usados para atribuir os recursos necessários para o aplicativo de transmissão.

> [!NOTE]
> Você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.
> 
> 

Quando você executar o comando, deve ver uma saída semelhante à seguinte:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote a ID do lote na última linha da saída (no exemplo, é '1'). Para verificar se o aplicativo é executado com sucesso, você pode examinar sua conta de armazenamento do Azure associada ao cluster e deverá ver a pasta **/EventCount/EventCount10** criada ali. Essa pasta deve conter blobs que capturam o número de eventos processados dentro do período de tempo especificado para o parâmetro **batch-interval-in-seconds**.

O aplicativo continuará a ser executado até você eliminá-lo. Para fazer isso, use o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Execute os aplicativos para receber os eventos em um Blob de armazenamento do Azure como JSON
Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputJSON.txt** são definidos da seguinte maneira:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto na etapa anterior. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.

 Depois de executar o comando, você pode examinar a sua conta de armazenamento do Azure associada ao cluster, e deverá ver a pasta **/EventStore10** criada ali. Abra qualquer arquivo prefixado com **part-** e você deverá ver os eventos processados em um formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Executar os aplicativos para receber os eventos em uma tabela Hive
Para executar o aplicativo que transmite eventos em uma tabela Hive, são necessários alguns componentes adicionais. Estes são:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Os arquivos **.jar**r estão disponíveis no seu cluster HDInsight Spark em `/usr/hdp/current/spark-client/lib`. O **hive-site.xml** está disponível em `/usr/hdp/current/spark-client/conf`.

Você pode usar [WinScp](http://winscp.net/eng/download.php) para copiar esses arquivos do cluster para seu computador local. Em seguida, você pode usar ferramentas para copiar esses arquivos para a sua conta de armazenamento associada ao cluster. Para obter mais informações sobre como carregar arquivos para a conta de armazenamento, consulte [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

Depois de copiar os arquivos da sua conta de armazenamento do Azure, abra um prompt de comando, navegue até o diretório em que instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e nome do cluster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputHive.txt** são definidos da seguinte maneira:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto nas etapas anteriores. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) ou a tabela do Hive de saída (EventHiveTable10) usadas como parâmetros. O aplicativo de streaming cria para você. Observe que a opção **jars** e **arquivos** inclui caminhos para os arquivos .jar e o hive-site.xml copiados para a conta de armazenamento.

Para verificar se a tabela de hive foi criada com êxito, é possível executar SSH no cluster e executar consultas de Hive. Para obter instruções, consulte [usar o Hive com Hadoop no HDInsight com SSH](hdinsight-hadoop-use-hive-ssh.md). Quando você estiver conectado usando SSH, poderá executar o comando a seguir para verificar se a tabela de Hive, **EventHiveTable10**, é criada.

    show tables;

Você deverá ver um resultado semelhante ao seguinte:

    OK
    eventhivetable10
    hivesampletable

Você também pode executar uma consulta SELECT para exibir o conteúdo da tabela.

    SELECT * FROM eventhivetable10 LIMIT 10;

Você verá algo semelhante ao mostrado a seguir:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Executar os aplicativos para receber os eventos em uma tabela de Banco de Dados SQL do Azure
Antes de executar essa etapa, verifique se que você tem um banco de dados SQL Azure criado. Para obter instruções, consulte [Criar um banco de dados SQL em minutos](../sql-database/sql-database-get-started.md). Para completar esta seção, você precisa de valores para nome do banco de dados, nome do servidor de banco de dados e credenciais de administrador de banco de dados como parâmetros. Porém, você não precisa criar a tabela de banco de dados. O aplicativo de transmissão a cria para você.

Abra um prompt de comando, navegue até o diretório no qual você instalou CURL e execute o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputSQL.txt** são definidos da seguinte maneira:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para verificar se o aplicativo é executado com êxito, você pode se conectar ao Banco de Dados SQL do Azure usando o SQL Server Management Studio. Para obter instruções sobre como fazer isso, consulte [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Quando você estiver conectado ao banco de dados, pode navegar para a tabela **EventContent** criada pelo aplicativo de transmissão. Você pode executar uma consulta rápida para obter os dados da tabela. Execute a consulta a seguir:

    SELECT * FROM EventCount

Você deverá ver uma saída semelhante ao seguinte:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com aprendizado de máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

