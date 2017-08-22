---
title: "Kit de Ferramentas do Azure para IntelliJ – Depurar aplicativos Spark remotamente por meio do SSH | Microsoft Docs"
description: "Instruções passo a passo sobre como usar as ferramentas do HDInsight no Kit de ferramentas do Azure para IntelliJ com o intuito de depurar aplicativos remotamente nos clusters do HDInsight por meio do SSH"
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
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar aplicativos Spark remotamente em um cluster HDInsight com o kit de ferramentas do Azure para IntelliJ por meio do SSH

Este artigo fornece instruções passo a passo sobre como usar as ferramentas do HDInsight no kit de ferramentas do Azure para IntelliJ com o intuito de depurar aplicativos remotamente em um cluster HDInsight. Você pode seguir um [vídeo](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) para depurar seu projeto.

**Pré-requisitos:**

* **Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ**. Elas fazem parte do Kit de ferramentas do Azure para IntelliJ. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Kit de Ferramentas do Azure para IntelliJ**. Use-o para criar aplicativos Spark para o cluster HDInsight. Para obter mais informações, siga as instruções em [Usar o Kit de ferramentas do Azure para IntelliJ para criar aplicativos Spark para cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Serviço SSH do HDInsight com gerenciamento de nome de usuário e senha.** Para obter mais informações, consulte [Conectar ao HDInsight (Hadoop) usando o SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usar o Túnel SSH para acessar a interface do usuário do Ambari na Web, JobHistory, NameNode, Oozie, entre outras](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Criar um aplicativo Spark Scala e configurá-lo para depuração remota
1. Inicie o IntelliJ IDEA e, então, crie um projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**. Este artigo usa uma **amostra de execução do Spark no Cluster HDInsight (Scala)** como exemplo.

     ![Criar um projeto de depuração](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - No painel esquerdo, escolha **HDInsight**.
   - No painel direito, selecione um modelo Java ou Scala com base em sua preferência. Escolha entre as seguintes opções: 
      - **Spark no HDInsight (Scala)**
      - **Spark no HDInsight (Java)**
      - **Amostra de execução do Spark no Cluster HDInsight (Scala)**
   - Ferramenta de build: o assistente de criação de projetos Scala dá suporte para o Maven ou para o SBT gerenciar as dependências e a criação de projetos do Scala. Selecione um de acordo com a necessidade.
2. Na próxima janela, forneça os detalhes do projeto.

   ![Selecionando o SDK do Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - Forneça um nome de projeto e o local do projeto.
   - Para o **SDK de projeto**, use o **Java 1.8** para o cluster do **Spark 2.x** ou o **Java 1.7** para o cluster do **Spark 1.x**.
   - Para a **versão do Spark**, o assistente de criação de projetos Scala integra a versão correta do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, escolha o **Spark 1.x**. Caso contrário, selecione **Spark 2.x.** Este artigo usa o **Spark 2.0.2 (Scala 2.11.8)** como exemplo.
3. Selecione **src** > **main** > **scala** para abrir seu código no projeto. Este artigo usa o script **SparkCore_wasbloTest** como um exemplo.
4. Para acessar o menu **Editar configurações**, selecione o ícone no canto superior direito. Nesse menu, você poderá criar ou editar as configurações de depuração remota.

   ![Editar configurações](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. Na janela **Configurações de execução/depuração**, clique no sinal de adição (**+**). Selecione a opção **Enviar trabalho do Spark**.

   ![Editar configurações](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. Insira as informações em **Nome**, **Cluster do Spark** e **Nome da classe principal**. Em seguida, selecione **Configurações avançadas**. 

   ![Executar configurações de depuração](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. Na caixa de diálogo **Configuração avançada de envio do Spark**, selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário e a senha do SSH ou use um arquivo de chave privada. Para salvar, selecione **OK**.

   ![Habilitar depuração remota do Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**. 
9. Após concluir as definições de configurações, você poderá executar o projeto no cluster remoto ou realizar a depuração remota.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>Saiba como realizar a depuração remota e a execução remota
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: realizar a execução remota
1. Para executar o projeto remotamente, selecione o ícone **Executar**.

   ![Clique no ícone de execução](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. A janela **Envio do HDInsight Spark** exibe o status de execução do aplicativo. Você pode monitorar o progresso do trabalho Scala com base nas informações aqui.

   ![Clique no ícone de execução](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: realizar a depuração remota
1. Configure um ponto de interrupção e, em seguida, selecione o ícone **Depurar**.

    ![Clique no ícone de depuração](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Depurador** no painel inferior. Você também verá os parâmetros de exibição e as informações de variáveis na janela **Variável**. Clique no ícone **Ignorar** para prosseguir para a próxima linha de código. Então, você poderá passar pelo código. Selecione o ícone **Retomar programa** para continuar a execução do código. Você pode examinar o status de execução na janela **Envio do HDInsight Spark**. 

   ![Guia depuração](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: realizar a depuração remota e a correção de bugs
Nesta seção, mostraremos como atualizar dinamicamente o valor da variável usando a capacidade de depuração do IntelliJ para uma correção simples. Para o exemplo de código a seguir, uma exceção será lançada porque o arquivo de destino já existe.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Para realizar a depuração remota e a correção de bugs
1. Configure dois pontos de interrupção e, em seguida, clique no ícone **Depurar** para iniciar o processo de depuração remota.

2. O código para no primeiro ponto de interrupção e as informações de parâmetro e de variáveis são mostradas na janela **Variável**. 

3. Clique no ícone **Retomar programa** para continuar. O código para no segundo ponto. A exceção está sendo capturada conforme o esperado.

  ![Gerar erro](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Clique no ícone **Retomar programa** novamente. A janela **Envio do HDInsight Spark** exibe um erro de falha na execução do trabalho.

  ![Envio de erro](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Para atualizar dinamicamente o valor da variável usando a capacidade de depuração do IntelliJ, selecione **Depurar** novamente. As janelas de variáveis aparecem novamente. 

6. Clique com o botão direito do mouse no destino na guia **Depurar** e, então, selecione **Definir valor**. Em seguida, insira um novo valor para a variável. Depois, selecione **Enter** para salvar o valor. 

  ![Definir valor](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Clique no ícone **Retomar programa** para continuar a execução do programa. Neste momento, a exceção não está sendo detectada. Você pode ver que o projeto é executado com êxito, sem exceções.

  ![Depurar sem exceção](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar projeto do Scala (vídeo): [Criar aplicativos Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (Vídeo): [Usar o kit de ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente no cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o kit de ferramentas do Azure para IntelliJ para depurar aplicativos remotamente no HDInsight Spark por meio da VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)
 
