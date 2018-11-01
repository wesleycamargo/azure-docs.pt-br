---
title: 'Usar o Azure Toolkit for IntelliJ: Criar aplicativos Spark para um cluster do HDInsight '
description: Use o Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: maxluk
ms.openlocfilehash: 51e1e7696ece46e63358b2ed6efa55bbf6ab01fd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420771"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Usar o Kit de Ferramentas do Azure para IntelliJ a fim de criar aplicativos Spark para um cluster HDInsight

Use o plug-in Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight diretamente do IDE (ambiente de desenvolvimento integrado) do IntelliJ. Você pode usar o plug-in destas maneiras:

* Desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Acessar os recursos de cluster Spark do Azure HDInsight.
* Desenvolver e executar um aplicativo Scala Spark localmente.

Para criar seu projeto, veja o vídeo [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Criar aplicativos Spark com o Kit de Ferramentas do Azure para IntelliJ).

> [!IMPORTANT]
> Você pode usar esse plug-in e enviar aplicativos somente para um cluster Spark do HDInsight no Linux.
> 

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Kit de desenvolvimento Oracle Java. Você pode instalá-lo do [site da Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. Este artigo usa a versão 2017.1. Você pode instalá-lo do [site da JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalar Kit de Ferramentas do Azure para IntelliJ
Para obter instruções de instalação, confira [Instalação do Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Introdução
O usuário pode [entrar a assinatura do Azure](#sign-in-to-your-azure-subscription), ou [vincular um cluster HDInsight](#link-a-cluster) usando a credencial de usuário/senha ou domínio unido do Ambari para iniciar.


## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Inicie o IDE do IntelliJ e abra o Azure Explorer. No menu **Exibir**, selecione **Janelas de Ferramentas** e **Azure Explorer**.
       
   ![O link do Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Clique com o botão direito do mouse no nó **Azure** e selecione **Entrar**.

1. Na caixa de diálogo **Entrada do Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![A caixa de diálogo Entrada do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Depois que você estiver conectado, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Escolha o botão **Selecionar**.

    ![A caixa de diálogo Selecionar Assinaturas](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters Spark do HDInsight em sua assinatura.
   
    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Para exibir os recursos (por exemplo, contas de armazenamento) associados ao cluster, você poderá expandir ainda mais um nó de nome de cluster.
   
    ![Um nó de nome de cluster expandido](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster HDInsight normal usando o nome de usuário gerenciado do Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, crie o vínculo usando o domínio e o nome de usuário, como user1@contoso.com. Also you can link Livy Service cluster.

1. Selecione **Vincular um cluster** no **Azure Explorer**.

   ![menu de contexto para vincular cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. You have two options to link clusters. 

   * For linking HDInsight cluster, choose **HDInsight Cluster** in field **Cluster Info**, enter **Cluster Name/URL**, **User Name**, and **Password**.

      ![link hdinsight cluster dialog](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * For linking Livy Service cluster, choose **Livy Service** in field **Cluster Info**, enter **Livy Endpoint**, **Cluster Name**. **O ponto final do fio** é opcional. No campo **Autenticação**, duas opções são fornecidas. Eles são **Autenticação Básica** e **Sem Autenticação**. Quando você seleciona **Autenticação Básica**, **Nome de Usuário** e **Senha** devem ser fornecidos. Você precisa verificar o nome de usuário e senha se tiver a falha de autenticação.
      
      ![Vincular a caixa de diálogo de cluster livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Se as informações de entrada estiverem corretas, será possível ver um cluster vinculado no nó **HDInsight**. Agora, você pode enviar um aplicativo para esse cluster vinculado.

   ![cluster vinculado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Também é possível desvincular um cluster a partir do **Azure Explorer**.
   
   ![cluster desvinculado](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala Spark em um cluster HDInsight Spark

1. Inicie o IDEA do IntelliJ e crie um projeto. No **novo projeto** caixa de diálogo, siga as etapas abaixo: 

   a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

   b. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

      * **Maven**, para obter suporte ao assistente de criação de projetos Scala
      * **SBT**, para gerenciar as dependências e a compilação no projeto Scala

    ![A caixa de diálogo Novo Projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Selecione **Avançar**.

1. O assistente de criação de projetos Scala detecta automaticamente se você instalou o plug-in Scala. Selecione **Instalar**.

   ![Verificação do plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Para baixar o plug-in Scala, selecione **OK**. Siga as instruções para reiniciar o IntelliJ. 

   ![A caixa de diálogo Instalação do plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. Na janela **Novo Projeto**, faça o seguinte:  

    ![Selecionando o SDK do Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Insira um nome e o local do projeto.

   b. Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster Spark 2.x ou selecione **Java 1.7** para o cluster Spark 1.x.

   c. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão apropriada do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x**. Esse exemplo usa o **Spark 2.0.2 (Scala 2.11.8)**.

1. Selecione **Concluir**.

1. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

   a. No menu **Arquivo**, escolha **Estrutura do Projeto**.

   b. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para exibir o artefato padrão criado. Você também pode criar seu próprio artefato selecionando o sinal de mais (**+**).

      ![Informações de artefato na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Adicione o código-fonte do aplicativo seguindo estas etapas:

   a. No Gerenciador de Projetos, clique com o botão direito do mouse em **src**, aponte para **Novo** e escolha **Classe do Scala**.
      
      ![Comandos para criar uma classe Scala do Explorador do Projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na caixa **Tipo** e selecione **OK**.
      
      ![Criar caixa de diálogo Nova Classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. No arquivo **MyClusterApp.scala** , cole o código a seguir. O código lê os dados no HVAC.csv (disponível em todos os clusters Spark do HDInsight), recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark
Depois de criar um aplicativo Scala, você poderá enviá-lo ao cluster.

1. No Explorador de projeto, localize um arquivo Java ou Scala e, em seguida, selecione **enviar aplicativo Spark para HDInsight** no menu de atalho.
    
      ![O comando Enviar Aplicativo Spark para HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela de caixa de diálogo de configuração, forneça os seguintes valores e clique em **SparkJobRun**.

      ![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.

    * Selecione um artefato do projeto IntelliJ ou uma opção do disco rígido.

    * **Nome da classe principal** campo: O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências (**...** ) e escolhendo outra classe.   

    * **As configurações de trabalho** campo: os valores padrão são definidos como imagem mostrada acima. Você pode alterar o valor ou adicionar nova chave/valor para o envio do trabalho. Para obter mais informações: [API REST do Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![O significado da configuração do trabalho da caixa de diálogo de Envio do Spark](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **Argumentos de linha de comando** campo: você pode inserir os valores de argumentos dividido pelo espaço para a classe principal, se necessário.

    * **Referenciado Jars** e **arquivos referenciados** campos: você pode inserir os caminhos para os Jars referenciados e os arquivos, se houver. Para obter mais informações: [configuração do Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) 

      ![O significado dos arquivos jar da caixa de diálogo de Envio do Spark](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > Para carregar seus referenciado JARs e os arquivos referenciados, consulte: [como carregar recursos de cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **Caminho de carregamento**: você pode indicar o local de armazenamento para o envio de recursos de projeto Jar ou Scala. Há três tipos de armazenamento com suporte: **BLOBs do Azure**, **sessão interativa de usar o Spark para carregar artefatos**, e **usar conta de armazenamento do cluster padrão**. Captura de tela abaixo é um exemplo de BLOBs do Azure.

        ![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Clique em **SparkJobRun** para enviar seu projeto para o cluster selecionado. A guia **Remote Spark Job no cluster** exibe o progresso da execução da tarefa na parte inferior. Você pode interromper o aplicativo clicando no botão vermelho. Para saber como acessar a saída do trabalho, confira a seção "Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ" mais adiante neste artigo.
      
     ![A janela Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicativos Spark local ou remotamente em um cluster HDInsight 
Também recomendamos outra forma de enviar o aplicativo Spark ao cluster. Você pode fazer isso definido os parâmetros no IDE **Executar/Depurar configurações**. Para saber mais, confira [Depurar aplicativos Spark local ou remotamente em um cluster HDInsight com o kit de ferramentas do Azure para IntelliJ por meio do SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ
Você pode executar várias operações usando o Kit de Ferramentas do Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark e escolha **Trabalhos**.  

    ![Nó de exibição de trabalho](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

    ![Detalhes do aplicativo](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Observação
    >

1. Para exibir informações básicas do trabalho em execução, passe o mouse sobre o grafo de trabalhos. Para exibir o grafo de estágios e as informações geradas pelos trabalhos, escolha um nó no grafo de trabalhos.

    ![Detalhes do estágio do trabalho](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Para exibir logs usados frequentemente, como *Stderr do Driver*, *Stdout do Driver* e *Informações do diretório*, escolha a guia **Log**.

    ![Detalhes do log](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Você também pode exibir a interface do usuário de histórico do Spark e a interface do usuário do YARN (no nível do aplicativo) selecionando um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, expanda o **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir IU do Histórico Spark**. 

1. Quando for solicitado, insira as credenciais do administrador do cluster, que você especificou ao configurar o cluster.

1. No painel do servidor de histórico do Spark, é possível usar o nome do aplicativo para procurar o que você acabou de executar. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Portanto, o nome do aplicativo Spark é **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**. 

1. Quando solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, o Kit de Ferramentas do Azure para IntelliJ listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas que deseja acessar. 

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Gerenciar Assinaturas**. 

1. Na caixa de diálogo, desmarque as caixas de seleção ao lado das assinaturas que você não deseja acessar e escolha **Fechar**. Você também poderá escolher **Sair** se quiser sair da sua assinatura do Azure.

## <a name="spark-console"></a>Console de ignição
Você pode executar o Spark Local Console (Scala) ou executar o Console de Sessão Interativa do Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Console(Scala) Local do Spark
1. Você precisa adicionar as dependências manualmente. No menu **Arquivo**->**Estruturas do Projeto**->**Configurações do Projeto**->**Bibliotecas** e clique em **+**, escolha **De Maven...**. Em seguida, insira **org.apache.spark:spark-repl_2.11:2.1.0** na caixa de diálogo pop-up. Depois de adicionar as dependências nas bibliotecas, você precisa mover a dependência para a primeira linha em **Módulos** na janela **Estruturas do Projeto**. Antes de mover, alterar o **escopo** à **tempo de execução**.

    ![Console Local Adicionar Bibliotecas de Dependências](./media/apache-spark-intellij-tool-plugin/local-console-add-dependency-libraries.png)

    ![Mudança de Console local para a primeira linha](./media/apache-spark-intellij-tool-plugin/local-console-move-first-line.png)

2. Defina a configuração se você não tiver nenhuma antes. Na janela **Run / Debug Configurations**, clique em **+**->**Azure HDInsight Spark**, selecione a guia **Locally Run**, escolha a principal clique em **OK**.

    ![Definir a configuração Console local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
3. Abra o arquivo correspondente ao seu arquivo de classe principal e clique com o botão direito do mouse em **Spark Console** e, em seguida, clique em **Executar Spark Local Console (Scala)**. Ou vá para o menu **ferramentas**->**Console Spark**->**Spark de execução Local Console(Scala)** para iniciar o console.

    ![Ponto de entrada de Local do Spark](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

4. Depois de iniciar o console local com êxito. Ele se parece com abaixo. Você pode fazer algo desejado. Por exemplo, digite **sc.appName**, pressione ctrl + Enter, em seguida, o resultado será mostrado.

    ![Resultado do Console local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Console de Sessão Interativa Spark Livy (Scala)
É suportado apenas no IntelliJ 2018.2.

1. Defina a configuração se você não tiver nenhuma antes. Na **executar/depurar configurações** janela, clique em **+** -> **Azure HDInsight Spark**, selecione guia **executar remotamente em Cluster** , escolha o nome do cluster e a classe principal, clique **Okey**.

    ![Console interativo Adicionar entrada de configuração](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Definir a configuração Console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Abra o arquivo correspondente à sua classe principal e clique com o botão direito em **Spark Console** e, em seguida, clique em **Executar o Console de Sessão Interativa do Spark Livy (Scala)**. Ou vá para o menu **ferramentas**, em seguida, clique em **Spark Console**, em seguida, **executar Spark Livy interativo sessão Console(Scala)** para iniciar o console.

3. Depois de iniciar o console com sucesso, você pode fazer o que quiser. Por exemplo, digite **sc.appName**, pressione ctrl + Enter, em seguida, o resultado será mostrado.

    ![Resultado do Console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar o Kit de Ferramentas do Azure para IntelliJ
Você pode converter os aplicativos Scala Spark existentes criados no IDEA do IntelliJ para serem compatíveis com o Kit de Ferramentas do Azure para IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos a um cluster Spark do HDInsight.

1. Para um aplicativo Scala Spark existente criado no IDEA do IntelliJ, abra o arquivo .iml associado.

1. Há um elemento **module** no nível de raiz, como o seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , de modo que o elemento **module** seja semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Salve as alterações. Seu aplicativo deve ser compatível com o Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up tem a opção **Enviar Aplicativo Spark ao HDInsight**.

## <a name="troubleshooting"></a>solução de problemas

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Erro na execução local: *Use um tamanho de heap maior*
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
Ao vincular um cluster, sugiro que você forneça credenciais de armazenamento.

![Vincular cluster, fornecer credencial de armazenamento](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Há dois modos para enviar os trabalhos. Se a credencial de armazenamento for fornecida, o modo de lote será usado para enviar o trabalho. Caso contrário, o modo interativo será usado. Se o cluster estiver ocupado, poderá receber o erro abaixo.

![IntelliJ recebe um erro quando o cluster estiver ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![IntelliJ recebe um erro quando o cluster estiver ocupado](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, não há suporte para exibir saídas do Spark diretamente.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar esse plug-in, envie-nos um email para hdivstool@microsoft.com.

## <a name="seealso"></a>Próximas etapas
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar projeto do Scala (vídeo): [Criar aplicativos Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente no cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
