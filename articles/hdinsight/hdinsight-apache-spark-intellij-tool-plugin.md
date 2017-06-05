---
title: Kit de Ferramentas do Azure para IntelliJ a fim de criar aplicativos Scala para Spark do HDInsight | Microsoft Docs
description: "Use o Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los para um cluster Spark do HDInsight."
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
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e6e005e07b35bbaffebf1efa216feebafc99df8b
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Usar o Kit de ferramentas do Azure para IntelliJ para criar aplicativos Spark para cluster HDInsight

Use o plug-in do Kit de Ferramentas do Azure para IntelliJ para desenvolver aplicativos Spark escritos em Scala e enviá-los para um cluster Spark do HDInsight, diretamente do IDE IntelliJ. Você pode usar o plug-in de várias maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

Você também pode acompanhar um [video](https://mix.office.com/watch/1nqkqjt5xonza) para começar.

> [!IMPORTANT]
> Esse plug-in pode ser usado para criar e enviar aplicativos apenas para um cluster Spark do HDInsight no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de desenvolvimento Oracle Java. Você pode instalá-lo do [site da Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IntelliJ IDEA. Este artigo usa a versão 15.0.1. Você pode instalá-lo do [site da JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalar Kit de Ferramentas do Azure para IntelliJ
Para obter instruções de instalação, confira [Instalando o Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure
1. Inicie o IDE IntelliJ e abra o Azure Explorer. No menu **Exibir**, clique em **Janelas de Ferramenta** e clique em **Azure Explorer**.
   
    ![Comandos selecionados no menu Exibir](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Clique com o botão direito do mouse no nó **Azure** e clique em **Entrar**.
3. Na caixa de diálogo **Entrada do Azure**, clique em **Entrar** e insira suas credenciais do Azure.
   
    ![Caixa de diálogo Entrar no Azure](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Após a entrada, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **Selecionar** para fechar a caixa de diálogo.

    ![Caixa de diálogo Selecionar Assinaturas](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters Spark do HDInsight da sua assinatura.
   
    ![Clusters Spark do HDInsight no Azure Explorer](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
    ![Expandindo um nome de cluster para ver recursos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark
1. Inicie o IntelliJ IDEA e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça as opções a seguir e clique em **Avançar**.
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Spark no HDInsight (Scala)**.
   
    ![Caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
2. Na próxima janela, forneça os seguintes detalhes do projeto e clique em **Concluir**.
   
   * Forneça um nome de projeto e o local do projeto.
   * Para o **SDK do projeto**, use o Java 1.8 para um cluster Spark 1.6 ou Spark 2.0.
   * Para **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada.
     * Escolha **JDK 1.8 e Scala 2.11.x** se desejar enviar o trabalho para um cluster Spark 2.0.
     * Escolha **JDK 1.8 (nível de linguagem 7) e Scala 2.10.x** se quiser enviar o trabalho a um cluster Spark 1.6.

        ![Selecionando a versão do Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Para o **SDK do Spark**, baixe e use o SDK do [GitHub] (http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Observe que spark-assembly-2.0.0-hadoop2.7.0-snapshot é para um cluster Spark 2.0 e spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar é para um cluster Spark 1.6. 

     Você pode usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), mas verifique se instalou o repositório Maven correto para desenvolver aplicativos Spark. Por exemplo, verifique se instalou a parte Streaming do Spark se você estiver usando o Streaming do Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 para um cluster Spark 1.6 e marcado como Scala 2.11 para um cluster Spark 2.0.
     
       ![Selecionando o SDK do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
3. O projeto do Spark cria automaticamente um artefato para você. Para ver o artefato, siga estas etapas:
   
   1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
   2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para ver o artefato padrão criado. Você também pode criar seu próprio artefato clicando no ícone **+**.
      
       ![Informações de artefato na caixa de diálogo](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
4. Na caixa de diálogo **Estrutura do Projeto**, clique em **Projeto**. Se o **SDK do Projeto** for definido como 1.8, defina **Nível de linguagem do projeto** como **7 - Diamonds, ARM, multi-catch, etc**. (É opcional para um cluster Spark 2.0.)
   
    ![Definindo o nível de linguagem do projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Adicione o código-fonte do aplicativo.
   
   1. No Gerenciador de Projetos, clique com o botão direito do mouse em **src**, aponte para **Novo** e clique em **Classe do Scala**.
      
       ![Comandos para criar uma classe Scala do Explorador do Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na caixa **Variante** e clique em **OK**.
      
       ![Criar caixa de diálogo Nova Classe Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. No arquivo **MyClusterApp.scala** , cole o código a seguir. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows that have only one digit in the seventh column in the CSV file
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

6. Execute o aplicativo em um cluster HDInsight Spark.
   
   1. No Explorador de Projetos, clique com o botão direito do mouse no nome do projeto e selecione **Enviar Aplicativo Spark para HDInsight**.
      
       ![Selecionando Enviar Aplicativo Spark para o HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Você recebe uma solicitação para inserir suas credenciais de assinatura do Azure. Na caixa de diálogo **Envio do Spark** , forneça os valores a seguir e clique em **Enviar**.
      
      * Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
      * Selecione um artefato do projeto IntelliJ ou uma opção do disco rígido.
      * Na caixa **Nome da classe principal**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png) ), selecione a classe principal no código-fonte do aplicativo, em seguida, clique em **OK**.
        
          ![Selecionar caixa de diálogo Classe Principal](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas restantes vazias.
        Depois de fornecer todas as entradas, a caixa de diálogo deve ser semelhante à imagem a seguir.
        
          ![Caixa de diálogo Envio do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
   3. A guia **Envio do Spark** na parte inferior da janela deve começar a exibir o progresso. Você também pode interromper o aplicativo clicando no botão vermelho na janela **Envio do Spark**.
      
       ![Janela Envio do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Na seção “Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ” mais adiante neste artigo, você aprenderá a acessar a saída do trabalho.

## <a name="choose-azure-data-lake-store-as-spark-scala-application-storage"></a>Escolha o Azure Data Lake Store como armazenamento do aplicativo Spark Scala
Se você quiser enviar um aplicativo para o Azure Data Lake Store, deverá escolher o modo **Interativo** durante o processo de entrada no Azure. 

   ![Opção interativa na entrada](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

Se você selecionar o modo **Automatizado**, obterá o seguinte erro:

   ![Erro de entrada](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ
Você pode executar várias operações usando o Kit de Ferramentas do Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark, em seguida, clique em **Trabalhos**.
2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Clique no nome do aplicativo do qual você deseja ver mais detalhes.
   
    ![Detalhes do aplicativo](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

As caixas **Erro**, **Log Driver**, **Saída** e **Livy Log** são preenchidas com base no aplicativo que você selecionar.

Você também pode abrir a IU do Histórico Spark e a IU YARN (no nível do aplicativo) clicando nos respectivos botões na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, expanda o **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir IU do Histórico Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas devem ter sido especificadas no provisionamento do cluster.
2. No painel do servidor de histórico do Spark, é possível usar o nome do aplicativo para procurar o que você acabou de executar. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**. 
2. Quando solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de provisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, o Kit de Ferramentas do Azure para IntelliJ listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas para as quais deseja acessar o cluster. 

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e clique em **Gerenciar Assinaturas**. 
2. Na caixa de diálogo, desmarque as caixas de seleção da assinatura que você não deseja acessar e clique em **Fechar**. Você também poderá clicar em **Sair** se quiser fazer sair da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente
Você pode usar o Kit de Ferramentas do Azure para IntelliJ para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como um contêiner de armazenamento e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque WinUtils.exe está ausente no Windows. 

Para solucionar esse erro, você deve [baixar o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local
1. Inicie o IntelliJ IDEA e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça as opções a seguir e clique em **Avançar**.
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)**.
   
    ![Seleções na caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
2. Na próxima janela, forneça os seguintes detalhes do projeto e clique em **Concluir**.
   
   * Forneça um nome de projeto e o local do projeto.
   * Para **SDK do Projeto**, forneça uma versão de Java superior à 7.
   * Para o **SDK do Scala**, clique em **Criar**, clique em **Baixar** e selecione a versão do Scala a ser usada: Scala 2.11.x para Spark 2.0 e Scala 2.10.x para Spark 1.6.
     
       ![Selecionando a versão do Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * Para o **SDK do Spark**, baixe e use o SDK no [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Você pode usar o [Repositório do Spark Maven](http://mvnrepository.com/search?q=spark), mas verifique se instalou o repositório Maven correto para desenvolver aplicativos Spark. Por exemplo, verifique se instalou a parte Streaming do Spark se você estiver usando o Streaming do Spark. Além disso, verifique se você está utilizando o repositório marcado como Scala 2.10 para um cluster Spark 1.6 e marcado como Scala 2.11 para um cluster Spark 2.0.
     
       ![Selecionando o SDK do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
3. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.
   
    ![Local do LogQuery](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Clique com o botão direito do mouse no aplicativo **LogQuery** e clique em **Executar 'LogQuery'**. Na guia **Executar** na parte inferior, você verá uma saída mais ou menos assim.
   
   ![Resultado da execução local do aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar o Kit de Ferramentas do Azure para IntelliJ
Você pode converter os aplicativos Spark Scala existentes criados no IntelliJ IDEA para serem compatíveis com o Kit de Ferramentas do Azure para IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos a um cluster Spark do HDInsight.

1. Para um aplicativo Scala Spark existente criado pelo IntelliJ IDEA, abra o arquivo .iml associado.
2. No nível raiz, você vê um elemento **module** como o seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , de modo que o elemento **module** seja semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
3. Salve as alterações. Seu aplicativo deve ser compatível com o Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up tem a opção **Enviar Aplicativo Spark ao HDInsight**.

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

Esses erros acontecem porque o tamanho do heap não é grande o suficiente para Spark ser executado. (O Spark requer pelo menos 471 MB. Você pode obter mais detalhes de [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Uma solução simples é usar um SDK do Java de 64 bits. Você também pode alterar as configurações da JVM no IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Adicionando opções à caixa "Opções de VM" no IntelliJ](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, não há suporte para exibir saídas do Spark diretamente.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar esse plug-in, fique à vontade para enviar um email para hdivstool@microsoft.com.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o Kit de Ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)


