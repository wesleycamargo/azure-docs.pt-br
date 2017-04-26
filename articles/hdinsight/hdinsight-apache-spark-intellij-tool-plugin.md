---
title: Usar o Kit de ferramentas do Azure para IntelliJ para criar aplicativos Scala para Spark | Microsoft Docs
description: "Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para desenvolver aplicativos Spark escritos em Scala e enviá-los para um cluster HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 494545ae20e0b766a3787ae462d5d0f4331853b1
ms.lasthandoff: 04/06/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Usar o Kit de ferramentas do Azure para IntelliJ para criar aplicativos Spark para cluster HDInsight

Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para desenvolver aplicativos Spark escritos em Scala e enviá-los para um cluster HDInsight Spark, diretamente do IDE IntelliJ. Você pode usar o plug-in ferramentas do HDInsight de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

Você também pode acompanhar um video [aqui](https://mix.office.com/watch/1nqkqjt5xonza) para começar.

> [!IMPORTANT]
> Essa ferramenta pode ser usada para criar e enviar aplicativos apenas para um cluster HDInsight Spark no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Este artigo usa a versão 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Instalar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ
As Ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas do Azure para IntelliJ. Para obter instruções sobre como instalar o Kit de Ferramentas do Azure, veja [Instalando o Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="log-into-your-azure-subscription"></a>Faça logon em sua assinatura do Azure
1. Inicie o IDE IntelliJ e abra o Azure Explorer. No menu **Exibir** no IDE, clique em **Janelas de Ferramenta**, em seguida, clique em **Azure Explorer**.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Clique com o botão direito no nó **Azure** no **Azure Explorer**, em seguida, clique em **Gerenciar Assinaturas**.
3. Na caixa de diálogo **Gerenciar Assinaturas**, clique em **Entrar** e insira suas credenciais do Azure.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Após o logon, a caixa de diálogo **Gerenciar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **Fechar** na caixa de diálogo.
5. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters HDInsight Spark em sua assinatura.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark
1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Spark no HDInsight (Scala)**.
   * Clique em **Próximo**.
2. Na próxima janela, forneça os detalhes do projeto.
   
   * Forneça um nome de projeto e o local do projeto.
   * Para o **SDK do Projeto**, Java 1.7 ou superior para cluster do Spark 1.6 e Java 1.8 para cluster do Spark 2.0.
   * Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada.
   * * Escolha **JDK 1.8 e Scala 2.11.x** se desejar enviar trabalhos para o cluster do Spark 2.0.
   * * Escolha **JDK 1.7 ou superior e Scala 2.10.x** se desejar enviar trabalhos para o cluster do Spark 1.6.

        ![](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Para o **SDK do Spark**, baixe e use o SDK [aqui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) (spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar destina-se ao cluster do Spark 2.0 e spark-assembly-x.jar destina-se ao cluster do Spark 1.6). Você também pode ignorar isso e usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), no entanto, certifique-se de ter o repositório maven correto instalado para desenvolver aplicativos Spark. (Por exemplo, você precisará certificar-se de que a parte do Streaming Spark está instalada se estiver usando o Streaming Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 para o cluster Spark 1.6 e mercado como Scala 2.11 para cluster do Spark 2.0).
     
       ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
   * Clique em **Concluir**.
3. O projeto do Spark criará automaticamente um artefato para você. Para ver o artefato, siga estas etapas.
   
   1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
   2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para ver o artefato padrão criado.
      
       ![Criar JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
      Você também pode criar seu próprio artefato clicando no ícone **+** , realçado na imagem acima.
4. Na caixa de diálogo **Estrutura do Projeto**, clique em **Projeto**. Se o **SDK do Projeto** for definido como 1.8, defina **Nível de linguagem do projeto** como **7 – Diamonds, ARM, multi-catch etc** (é opcional para clusters do Spark 2.0).
   
    ![Definir o nível de linguagem do projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Adicione o código-fonte do aplicativo.
   
   1. No **Gerenciador de Projetos**, clique com o botão direito em **src**, aponte para **Novo** e clique em **Classe do Scala**.
      
       ![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, para **Variante** escolha **Objeto** e clique em **OK**.
      
       ![Adicionar código-fonte](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. No arquivo **MyClusterApp.scala** , cole o código a seguir. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sétima coluna no CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

1. Execute o aplicativo em um cluster HDInsight Spark.
   
   1. No **Explorador de Projetos**, clique com o botão direito no nome do projeto, em seguida, selecione **Enviar Aplicativo Spark para HDInsight**.
      
       ![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Você receberá uma solicitação para inserir suas credenciais de assinatura do Azure. Na caixa de diálogo **Envio do Spark** , forneça os valores a seguir.
      
      * Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
      * Você precisa selecionar um Artefato do projeto IntelliJ ou selecionar uma opção do disco rígido.
      * Na caixa de texto **Nome da classe principal**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png) ), selecione a classe principal no código-fonte do aplicativo, em seguida, clique em **OK**.
        
          ![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.
      * Depois de fornecer todas as entradas, a caixa de diálogo deve ser semelhante ao seguinte.
        
          ![Enviar aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      * Clique em **Enviar**.
   3. A guia **Envio do Spark** na parte inferior da janela deve começar a exibir o progresso. Você também pode interromper o aplicativo clicando no botão vermelho na janela "Envio do Spark".
      
       ![Resultado do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Na próxima seção, você aprenderá a acessar a saída do trabalho usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ.

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters HDInsight Spark usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ
Você pode executar várias operações usando as ferramentas do HDInsight, que fazem parte do Kit de ferramentas do Azure para IntelliJ.

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>Acessar a exibição de trabalho diretamente das Ferramentas do HDInsight
1. No **Azure Explorer**, expanda **HDInsight**, expanda o nome do cluster Spark, em seguida, clique em **Trabalhos**.
2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Clique no nome do aplicativo para o qual você deseja ver mais detalhes.
   
    ![Acessar a exibição de trabalhos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. As caixas da **Mensagem de Erro**, **Saída do Trabalho**, **Logs de Trabalho Livy** e **Logs do Driver Spark** são preenchidas com base no aplicativo selecionado.
4. Você também pode abrir a **IU do Histórico Spark**  e a **IU YARN** (no nível do aplicativo) clicando nos respectivos botões na parte superior da tela.

### <a name="access-the-spark-history-server"></a>Acessar o Servidor de Histórico do Spark
1. No **Azure Explorer**, expanda o **HDInsight**, clique com o botão direito no nome do cluster Spark, em seguida, selecione **Abrir IU do Histórico Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.
2. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código acima, defina o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Iniciar o portal do Ambari
No **Azure Explorer**, expanda o **HDInsight**, clique com o botão direito no nome do cluster Spark, em seguida, selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, as ferramentas do HDInsight listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, especifique as assinaturas das quais você deseja acessar o cluster. No **Azure Explorer**, clique com o botão direito do nó-raiz **Azure**, em seguida, clique em **Gerenciar Assinaturas**. Na caixa de diálogo, desmarque as caixas de seleção com base na assinatura que você não deseja acessar e clique em **Fechar**. Você também poderá clicar em **Sair** se quiser fazer logoff da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente
Você pode usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como o contêiner de armazenamento e podem ser executados e testados localmente.

### <a name="prerequisite"></a>Pré-requisito
Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) , que ocorre devido à ausência do WinUtils.exe no Windows. Para solucionar esse erro, você deve [baixar aqui o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local
1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.
   
    ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)**.
   * Clique em **Próximo**.
2. Na próxima janela, forneça os detalhes do projeto.
   
   * Forneça um nome de projeto e o local do projeto.
   * Para **SDK do Projeto**, forneça uma versão de Java superior à 7.
   * Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada.**Scala 2.11.x para Spark 2.0 e Scala 2.10.x para Spark 1.6**.
     
       ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * Para o **SDK do Spark**, baixe e use o SDK [deste local](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Você também pode ignorar isso e usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), no entanto, certifique-se de ter o repositório maven correto instalado para desenvolver aplicativos Spark. (Por exemplo, você precisará certificar-se de que a parte do Streaming Spark está instalada se estiver usando o Streaming Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 para o cluster Spark 1.6 e mercado como Scala 2.11 para cluster do Spark 2.0).
     
       ![Criar um aplicativo Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
   * Clique em **Concluir**.
3. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.
   
    ![Aplicativo Scala local](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Clique com o botão direito no aplicativo **LogQuery** e clique em **"Executar 'LogQuery'"**. Você verá uma saída como esta na guia **Executar** na parte inferior.
   
   ![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ
Você também pode converter os aplicativos Spark Scala existentes criados no IntelliJ IDEA para serem compatíveis com as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ. Isso permitirá que você use a ferramenta para enviar os aplicativos a um cluster HDInsight Spark. Faça isso executando as seguintes etapas:

1. Para um aplicativo Scala Spark existente criado usando IntelliJ IDEA, abra o arquivo .iml associado.
2. No nível raiz, você verá um elemento **module** como o seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
3. Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , de modo que o elemento **module** seja semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
4. Salve as alterações. Seu aplicativo deve ser compatível com as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up deve ter a opção para **Enviar Aplicativo Spark ao HDInsight**.

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>Erro "Use um tamanho de heap maior" na execução local
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

Isso ocorre simplesmente porque o tamanho do heap não é grande o suficiente para executar o Spark, já que o Spark requer pelo menos 471 MB (você poderá obter mais detalhes em [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) , se desejar). Uma solução simples é usar um SDK do Java de 64 bits. Você também pode alterar as configurações da JVM no IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Resultado da execução local do Aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>Comentários e problemas conhecidos
Atualmente, não há suporte para exibir saídas do Spark diretamente e estamos trabalhando nisso.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar essa ferramenta, fique à vontade para enviar um email no hdivstool em microsoft.com.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)


