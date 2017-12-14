---
title: "Kit de Ferramentas do Azure para IntelliJ – Depurar aplicativos Spark remotamente por meio do SSH | Microsoft Docs"
description: "Instruções passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos remotamente nos clusters do HDInsight por meio do SSH"
keywords: "depurar o intellij remotamente, depuração remota do intellij, ssh, intellij, hdinsight, depurar o intellij, depuração"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 6ea6d94453583c6b5ed680f96ebc43d33d0262a0
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depure aplicativos Spark local ou remotamente em um cluster HDInsight com o kit de ferramentas do Azure para IntelliJ por meio do SSH

Este artigo fornece instruções passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos remotamente em um cluster do HDInsight. Para depurar seu projeto, você também poderá exibir o vídeo [Depurar aplicativos do HDInsight Spark com o Kit de Ferramentas do Azure para IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ).

**Pré-requisitos**
* **Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ**. Essa ferramenta faz parte do Kit de Ferramentas do Azure para IntelliJ. Para mais informações, consulte [Instalar o Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). E o **Kit de Ferramentas do Azure para IntelliJ**. Use esse kit de ferramentas para criar aplicativos Spark para o cluster do HDInsight. Para obter mais informações, siga as instruções em [Usar o Kit de Ferramentas do Azure para IntelliJ para criar aplicativos Spark para um cluster do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Serviço SSH do HDInsight com gerenciamento de nome de usuário e senha**. Para obter mais informações, consulte [Conectar ao HDInsight (Hadoop) usando o SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usar o túnel SSH para acessar a interface do usuário Web do Ambari, JobHistory, NameNode, Oozie, entre outras](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Saiba como realizar a execução e depuração local
### <a name="scenario-1-create-a-spark-scala-application"></a>Cenário 1: criar um aplicativo Spark Scala 

1. Inicie o IDEA do IntelliJ e crie um projeto. Na caixa de diálogo **Novo Projeto** , faça o seguinte:

   a. Selecione **HDInsight**. 

   b. Selecione um modelo Java ou Scala com base em sua preferência. Selecione entre as seguintes opções:

      - **Spark no HDInsight (Scala)**

      - **Spark no HDInsight (Java)**

      - **Amostra do Spark no HDInsight (Scala)**

      Este exemplo usa um modelo de **Amostra do Spark no HDInsight (Scala)**.

   c. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

      - **Maven**, para obter suporte ao assistente de criação de projetos Scala

      -  **SBT**, para gerenciar as dependências e a compilação no projeto Scala 

      ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **Avançar**.     
 
2. Na janela **Novo Projeto** a seguir, faça o seguinte:

   ![Selecione o SDK do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster **Spark 2.x** ou selecione **Java 1.7** para o cluster **Spark 1.x**.

   c. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão correta do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior à versão 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x.** Esse exemplo usa o **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione **src** > **main** > **scala** para abrir seu código no projeto. Este exemplo usa o script **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Pré-requisito para Windows
Durante a execução do aplicativo Scala Spark local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está ausente no Windows. 

Para resolver esse erro, [baixe o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Cenário 2: executar a execução local
1. Abra o script **SparkCore_wasbloTest**, clique com o botão direito do mouse no editor de script e, em seguida, selecione a opção **Executar 'XXX [Trabalho do Spark]'** para realizar a execução local.
2. Assim que a execução local estiver concluída, você pode ver o arquivo de saída salvo no Explorador de projeto atual **dados** > **__padrão__**.

    ![Resultado da execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Nossas ferramentas definiram a configuração de execução local automaticamente quando você executar a execução local e depuração local. Abra a configuração **XXX [Trabalho do Spark]** no canto superior direito e você poderá ver o **XXX [Trabalho do Spark]** já criado em **Trabalho do Spark do Azure HDInsight**. Mude para a guia **Executar localmente**.

    ![Configuração de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variáveis de ambiente](#prerequisite-for-windows): se você já definiu a variável de ambiente do sistema **HADOOP_HOME** como **C:\WinUtils**, ela pode detectar automaticamente que não é necessário adicionar manualmente.
    - [Local de WinUtils.exe](#prerequisite-for-windows): se você não tiver definido a variável de ambiente do sistema, poderá encontrar o local clicando em seu botão.
    - Basta escolher uma das duas opções, e elas não são necessárias em MacOS e Linux.
4. Você também pode definir a configuração manualmente antes de executar a depuração e execução local. Na captura de tela acima, selecione o sinal de adição (**+**). Em seguida, selecione a opção **Trabalho do Spark do Azure HDInsight**. Insira as informações para **Nome**, **Nome da classe principal** para salvar, em seguida, clique no botão de execução local.

### <a name="scenario-3-perform-local-debugging"></a>Cenário 3: executar a depuração local
1. Abra o script **SparkCore_wasbloTest** e defina os pontos de interrupção.
2. Clique com o botão direito do mouse no editor de script, e, em seguida selecione a opção **Depurar '[Trabalho do Spark]XXX'** para realizar a depuração local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Saiba como realizar a execução e depuração remota
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: realizar a execução remota

1. Para acessar o menu **Editar Configurações**, selecione o ícone no canto superior direito. Nesse menu, você poderá criar ou editar as configurações de depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. Na caixa de diálogo **Configurações de Execução/Depuração**, selecione o sinal de mais (**+**). Em seguida, selecione a opção **Trabalho do Spark do Azure HDInsight**.

   ![Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Mude para a guia **Executar Remotamente em Cluster**. Insira as informações em **Nome**, **Cluster do Spark** e **Nome da classe principal**. Em seguida, selecione **Configurações avançadas**. Nossas ferramentas oferecem suporte à depuração com **Executores**. O **numExectors**, o valor padrão é 5. É melhor não definir como maior que 3.

   ![Executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. Na caixa de diálogo **Configuração avançada de envio do Spark**, selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário do SSH e insira uma senha ou use um arquivo de chave privada. Selecione **OK** para salvar a configuração. Se desejar executar a depuração remota, será necessário configurá-la. Não será necessário configurá-la se quiser usar apenas a execução remota.

   ![Habilitar depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**. 

6. Após concluir as definições de configurações, você poderá executar o projeto no cluster remoto ou realizar a depuração remota.
   
   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Clique no botão **Desconectar** para ocultar os logs de envio do painel esquerdo. Eles continuarão sendo executados em segundo plano.

   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: realizar a depuração remota
1. Configure pontos de interrupção e, em seguida, clique no ícone **Depuração remota**. A diferença com envio remoto é que o nome de usuário/senha de SSH devem ser configurados.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Driver** e duas guias **Executor** no painel **Depurador**. Selecione o ícone **Retomar Programa** para continuar a execução do código, que alcança o próximo ponto de interrupção. Você precisará mudar para a guia **Executor** correta para localizar o executor de destino para depurar. Você pode visualizar os logs de execução na guia **Console** correspondente.

   ![Guia depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: realizar a depuração remota e a correção de bugs
1. Configure dois pontos de interrupção e, em seguida, selecione o ícone **Depurar** para iniciar o processo de depuração remota.

2. O código para no primeiro ponto de interrupção e as informações de parâmetro e de variáveis são mostradas no painel **Variáveis**. 

3. Selecione o ícone **Retomar Programa** para continuar. O código para no segundo ponto. A exceção é capturada conforme o esperado.

   ![Gerar erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Selecione o ícone **Retomar Programa** novamente. A janela **Envio do HDInsight Spark** exibe um erro de “falha na execução do trabalho”.

   ![Envio de erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Para atualizar dinamicamente o valor da variável usando a capacidade de depuração do IntelliJ, selecione **Depurar** novamente. O painel **Variáveis** é exibido novamente. 

6. Clique com o botão direito do mouse no destino na guia **Depurar** e, então, selecione **Definir valor**. Em seguida, insira um novo valor para a variável. Depois, selecione **Enter** para salvar o valor. 

   ![Definir valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Selecione o ícone **Retomar Programa** para continuar a execução do programa. Neste momento, nenhuma exceção é detectada. Você pode ver que o projeto é executado com êxito, sem exceções.

   ![Depurar sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Próximas etapas
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar projeto do Scala (vídeo): [Criar aplicativos Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente em um cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o Kit de Ferramentas do Azure para IntelliJ para criar aplicativos Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
