---
title: 'Azure Toolkit for IntelliJ: Criar aplicativos Spark para o cluster HDInsight '
description: Use o Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: a22dd1114b6ad49695b1ce7cab2ff26f23b7e1be
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447847"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Use o Azure Toolkit for IntelliJ para criar aplicativos do Apache Spark para um cluster do HDInsight

Use o plug-in do Azure Toolkit for IntelliJ para desenvolver [ aplicativos do Apache Spark ](https://spark.apache.org/) escritos em [ Scala ](https://www.scala-lang.org/) e depois enviá-los para um cluster do HDInsight Spark diretamente do ambiente de desenvolvimento integrado IntelliJ (IDE). Você pode usar o plug-in destas maneiras:

* Desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Acessar os recursos de cluster Spark do Azure HDInsight.
* Desenvolver e executar um aplicativo Scala Spark localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Kit de desenvolvimento Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  Este tutorial usa o Java versão 8.0.202.
* IntelliJ IDEA. Este artigo usa o [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Confira [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalação do Azure Toolkit for IntelliJ).
* WINUTILS.EXE.  Confira [problemas ao executar o Hadoop no Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in Scala para IntelliJ IDEA
Siga estas etapas para instalar o plug-in Scala:

1. Abra o IntelliJ IDEA.

2. Na tela de boas-vindas, navegue até **Configurar** > **Plug-ins** para abrir a janela **Plug-ins**.
   
    ![Habilitar plug-in do scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Selecione **Instalar** para o plug-in Scala caracterizado na nova janela.  

    ![Instalar o plug-in do scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Depois que o plug-in foi instalado com êxito, você deve reiniciar o IDE.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala Spark para um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.

2. Selecione **Azure Spark/HDInsight** no painel esquerdo.

3. Selecione **Projeto Spark (Scala)** na janela principal.

4. Na lista suspensa **Ferramenta de build**, selecione uma das seguintes opções:
      * **Maven** para obter suporte ao assistente de criação de projetos Scala.
      * **SBT** para gerenciar as dependências e para criar no projeto Scala.

    ![A caixa de diálogo Novo Projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Avançar**.

6. Na janela **Novo Projeto**, forneça as seguintes informações:  

    |  Propriedade   | DESCRIÇÃO   |  
    | ----- | ----- |  
    |Nome do projeto| Insira um nome.  Este tutorial usa `myApp`.|  
    |Local do&nbsp;projeto| Insira o local desejado para salvar o projeto.|
    |SDK do projeto| Isso poderá ficar em branco no primeiro uso do IDEA.  Selecione **Novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada para o SDK do Spark e o SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x**. Esse exemplo usa o **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Selecionando o SDK do Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Selecione **Concluir**.  Pode levar alguns minutos antes que o projeto fique disponível.

8. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

    a. Na barra de menus, navegue até **Arquivo** > **Estrutura do projeto...**.

   b. Na janela **Estrutura do Projeto**, selecione **Artefatos**.  

   c. Selecione **Cancelar** depois de exibir o artefato.

      ![Informações de artefato na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Adicione o código-fonte do aplicativo seguindo estas etapas:

     a. Em Projeto, navegue até **myApp** > **src** > **principal** > **scala**.  

    b. Clique com o botão direito do mouse em **scala** e, em seguida, navegue até **Novo** > **classe Scala**.

   ![Comandos para criar uma classe Scala do Projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na lista suspensa **Tipo** e selecione **OK**.

     ![Criar caixa de diálogo Nova Classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O arquivo **myApp.scala** então se abre na exibição principal. Substitua o código padrão pelo código localizado abaixo:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    O código lê os dados no HVAC.csv (disponível em todos os clusters Spark do HDInsight), recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em `/HVACOut` no contêiner padrão de armazenamento do cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Conectar-se ao cluster HDInsight
O usuário pode [entrar a assinatura do Azure](#sign-in-to-your-azure-subscription) ou [vincular um cluster HDInsight](#link-a-cluster) usando a credencial de usuário/senha ou domínio unido do Ambari para conectar seu cluster do HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.
       
   ![O link do Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. No Azure Explorer, clique com o botão direito do mouse no nó **Azure** e, em seguida, selecione **Entrar**.

3. Na caixa de diálogo **Entrada do Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![A caixa de diálogo Entrada do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Depois que você estiver conectado, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Selecione sua assinatura e, em seguida, selecione o botão **Selecionar**.

    ![A caixa de diálogo Selecionar Assinaturas](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Em **Azure Explorer**, expanda **HDInsight** para exibir os clusters Spark do HDInsight em sua assinatura.

    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Para exibir os recursos (por exemplo, contas de armazenamento) associados ao cluster, você poderá expandir ainda mais um nó de nome de cluster.

    ![Um nó de nome de cluster expandido](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster HDInsight usando o nome de usuário gerenciado do Apache Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, crie o vínculo usando o domínio e o nome de usuário, como user1@contoso.com. Also you can link Livy Service cluster.

1. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

2. No Azure Explorer, clique com o botão direito do mouse em **HDInsight** nó e selecione **Vincular um Cluster**.

   ![menu de contexto para vincular cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. As opções disponíveis na janela **Vincular um Cluster** vão variar conforme o valor selecionado na lista suspensa **Tipo de Recurso de Link**.  Insira seus valores e, em seguida, selecione **OK**.

    * **Cluster do HDInsight**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de Recurso de Link|Selecione **Cluster do HDInsight** na lista suspensa.|
        |Nome/URL do cluster| Insira o nome do cluster.|
        |Tipo de autenticação| Deixe como **Autenticação Básica**|
        |Nome do Usuário| Insira o nome de usuário do cluster, o padrão é admin.|
        |Senha| Insira a senha do nome de usuário.|
    
        ![link hdinsight cluster dialog](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço Livy**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de Recurso de Link|Selecione **Serviço Livy** na lista suspensa.|
        |Ponto de Extremidade do Livy| Inserir o Ponto de Extremidade Livy|
        |Nome do cluster| Insira o nome do cluster.|
        |Ponto de Extremidade do Yarn|Opcional.|
        |Tipo de autenticação| Deixe como **Autenticação Básica**|
        |Nome do Usuário| Insira o nome de usuário do cluster, o padrão é admin.|
        |Senha| Insira a senha do nome de usuário.|

        ![Vincular a caixa de diálogo de cluster livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Você pode ver seu cluster vinculado do nó do **HDInsight**.

   ![cluster vinculado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Também é possível desvincular um cluster a partir do **Azure Explorer**.

   ![cluster desvinculado](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark
Depois de criar um aplicativo Scala, você poderá enviá-lo ao cluster.

1. De Projeto, navegue até **myApp** > **src** > **principal** > **scala**  > **myApp**.  Clique com o botão direito do mouse em **myApp** e selecione **Enviar Aplicativo Spark** (provavelmente estará localizado na parte inferior da lista).
    
      ![O comando Enviar Aplicativo Spark para HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela de diálogo **Enviar Aplicativo do Spark**, selecione **1. Spark no HDInsight**.

3. Na janela **Editar configuração**, forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Clusters Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Selecione um Artefato para enviar|Deixe a configuração padrão.|
    |Nome de classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências (**...** ) e escolhendo outra classe.|
    |Configurações de trabalho|Você pode alterar os valores de e/ou as chaves padrão. Para obter mais informações, confira [API REST do Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos de linha de comando|Você pode inserir argumentos separados por espaço para a classe principal se necessário.|
    |Arquivos Referenciados e Jars Referenciados|Você pode inserir os caminhos para os Jars referenciados e arquivos, se houver. Para mais informações: [Configuração do Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Confira também [Como carregar recursos para o cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Armazenamento de Upload de Trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **Usar Blob do Azure para carregar** na lista suspensa.|
    |Conta de armazenamento|Insira sua conta de armazenamento.|
    |Chave de Armazenamento|Insira sua chave de armazenamento.|
    |Contêiner de armazenamento|Selecione seu contêiner de armazenamento na lista suspensa depois que **Conta de Armazenamento** e **Chave de Armazenamento** tiverem sido inseridas.|

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Selecione **SparkJobRun** para enviar seu projeto para o cluster selecionado. A guia **Remote Spark Job no cluster** exibe o progresso da execução da tarefa na parte inferior. Você pode interromper o aplicativo clicando no botão vermelho. Para saber como acessar a saída do trabalho, confira a seção "Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ" mais adiante neste artigo.  
      
    ![A janela Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicativos do Apache Spark local ou remotamente em um cluster do HDInsight 
Também recomendamos outra forma de enviar o aplicativo Spark ao cluster. Você pode fazer isso definido os parâmetros no IDE **Executar/Depurar configurações**. Para obter mais informações, consulte [Depurar aplicativos do Apache Spark local ou remotamente em um cluster do HDInsight com o Azure Toolkit for IntelliJ por meio do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ
Você pode executar várias operações usando o Kit de Ferramentas do Azure para IntelliJ.  A maioria das operações é iniciada pelo **Azure Explorer**.  Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho

1. No Azure Explorer, navegue até **HDInsight** > \<Seu Cluster> > **Trabalhos**.

    ![Nó de exibição de trabalho](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

    ![Detalhes do aplicativo](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Para exibir informações básicas do trabalho em execução, passe o mouse sobre o grafo de trabalhos. Para exibir o grafo de estágios e as informações geradas pelos trabalhos, escolha um nó no grafo de trabalhos.

    ![Detalhes do estágio do trabalho](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para exibir logs usados frequentemente, como *Stderr do Driver*, *Stdout do Driver* e *Informações do diretório*, escolha a guia **Log**.

    ![Detalhes do log](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Você também pode exibir a interface do usuário de histórico do Spark e a interface do usuário do YARN (no nível do aplicativo) selecionando um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark

1. No Azure Explorer, expanda o **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir interface do usuário do Histórico Spark**.  
2. Quando for solicitado, insira as credenciais do administrador do cluster, que você especificou ao configurar o cluster.

3. No painel do servidor de histórico do Spark, é possível usar o nome do aplicativo para procurar o que você acabou de executar. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("myApp")`. Portanto, o nome do aplicativo Spark é **myApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari

1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**.  

2. Quando solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, o Kit de Ferramentas do Azure para IntelliJ listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas que deseja acessar.  

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Selecionar Assinaturas**.  

2. Na janela **Selecionar Assinaturas**, desmarque as caixas de seleção ao lado das assinaturas que você não deseja acessar e, em seguida, selecione **Fechar**.

## <a name="spark-console"></a>Console de ignição
Você pode executar o Spark Local Console (Scala) ou executar o Console de Sessão Interativa do Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Console(Scala) Local do Spark
Verifique se você atendeu ao pré-requisito WINUTILS.EXE.

1. Na barra de menus, navegue até **Executar** > **Editar configurações...**.

2. Na janela **Executar/depurar configurações**, no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione a guia **Executar Localmente**.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Classe principal do trabalho|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências (**...** ) e escolhendo outra classe.|
    |Variáveis de ambiente|Garanta que o valor para HADOOP_HOME esteja correto.|
    |Localização de WINUTILS.exe|Garanta que o caminho esteja correto.|

    ![Definir a configuração Console local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. De Projeto, navegue até **myApp** > **src** > **principal** > **scala**  > **myApp**.  

6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console Local do Spark (Scala)**.

7. Em seguida, duas caixas de diálogo poderão ser exibidas para perguntar se você deseja consertar as dependências automaticamente. Neste caso, selecione **Conserto Automático**.

    ![Spark automática Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark automática Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. O console deve ser semelhante à imagem abaixo. No tipo de janela de console, digite `sc.appName` e, em seguida, pressione ctrl+Enter.  O resultado será mostrado. Você pode encerrar o console local clicando no botão vermelho.

    ![Resultado do Console local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Console de Sessão Interativa Spark Livy (Scala)
Ele tem suporte apenas no IntelliJ 2018.2 e 2018.3.

1. Na barra de menus, navegue até **Executar** > **Editar configurações...**.

2. Na janela **Executar/depurar configurações**, no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione a guia **Executar Remotamente no Cluster**.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Clusters Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Nome de classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências (**...** ) e escolhendo outra classe.|

    ![Definir a configuração Console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. De Projeto, navegue até **myApp** > **src** > **principal** > **scala**  > **myApp**.  

6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console de Sessão Interativa do Spark Livy (Scala)**.

7. O console deve ser semelhante à imagem abaixo. No tipo de janela de console, digite `sc.appName` e, em seguida, pressione ctrl+Enter.  O resultado será mostrado. Você pode encerrar o console local clicando no botão vermelho.

    ![Resultado do Console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para o Console do Spark

É conveniente para você prever o resultado do script enviando algum código para o console local ou Console (Scala) de sessão interativa Livy. Você pode realçar algum código no arquivo de Scala e então clicar com o botão direito do mouse em **Enviar Seleção para o Console do Spark**. Os códigos selecionados serão enviados para o console e serão executados. O resultado será exibido após o código no console. O console verificará os erros, se existente.  

   ![Enviar seleção para o Console do Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar o Kit de Ferramentas do Azure para IntelliJ

Você pode converter os aplicativos Scala Spark existentes criados no IDEA do IntelliJ para serem compatíveis com o Kit de Ferramentas do Azure para IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos a um cluster Spark do HDInsight.

1. Para um aplicativo Scala Spark existente criado no IDEA do IntelliJ, abra o arquivo .iml associado.

1. Há um elemento **module** no nível de raiz, como o seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , de modo que o elemento **module** seja semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Salve as alterações. Seu aplicativo deve ser compatível com o Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Projeto. Agora, o menu pop-up tem a opção **Enviar Aplicativo Spark ao HDInsight**.

## <a name="troubleshooting"></a>solução de problemas

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Erro na execução local: *Use um tamanho de heap maior*
No Spark 1.6, se você estiver usando um SDK Java de 32 bits durante a execução local, poderá encontrar os seguintes erros:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Esses erros acontecem porque o tamanho do heap não é grande o suficiente para Spark ser executado. O Spark requer pelo menos 471 MB. (Para saber mais, confira [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Uma solução simples é usar um SDK do Java de 64 bits. Você também pode alterar as configurações da JVM no IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Adicionando opções à caixa "Opções de VM" no IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Perguntas frequentes
Se o cluster estiver ocupado, poderá receber o erro abaixo.

![IntelliJ recebe um erro quando o cluster estiver ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![IntelliJ recebe um erro quando o cluster estiver ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, não há suporte para exibir saídas do Spark diretamente.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar esse plug-in, envie-nos um email para hdivstool@microsoft.com.

## <a name="seealso"></a>Próximas etapas
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Crie o projeto Scala (vídeo): [Criar aplicativos Scala do Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [ Use o Azure Toolkit for IntelliJ para depurar os aplicativos do Apache Spark remotamente por meio do SSH ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
