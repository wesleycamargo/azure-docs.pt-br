---
title: Enviar trabalhos a partir das Ferramentas do R para Visual Studio - Azure HDInsight | Microsoft Docs
description: "Envie trabalhos de R em sua máquina local do Visual Studio para um cluster HDInsight."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 1a82ba7790f739768156a8bee33a74d7130e24e1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Enviar trabalhos a partir das Ferramentas do R para Visual Studio

[Ferramentas R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) é uma extensão gratuita e de código-fonte aberto para as edições Community (gratuita), Professional e Enterprise do [Visual Studio 2017](https://www.visualstudio.com/downloads/), e [Visual Studio 2015 Atualização 3](http://go.microsoft.com/fwlink/?LinkId=691129) ou superior.

RTVS aprimora seu fluxo de trabalho de R, oferecendo ferramentas como o [janela de R Interativo](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (conclusão de código), [plotar visualização](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) por meio de bibliotecas de R como ggplot2 e ggviz, [depuração de código de R](https://docs.microsoft.com/visualstudio/rtvs/debugging)e muito mais.

## <a name="set-up-your-environment"></a>Configure seu ambiente

1. Instalar [Ferramentas do R para Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalando o RTVS no Visual Studio de 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecione a carga de trabalho *ciência de dados e aplicativos analíticos*, selecione as opções **suporte a idioma R**, **suporte de tempo de execução para o desenvolvimento de R**, e **Microsoft R Client**.

3. Você precisa ter as chaves públicas e privadas para autenticação de SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale o [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) em seu computador. R Server fornece as funções [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) e `RxSpark`.

5. Instale [PuTTY](http://www.putty.org/) para fornecer um contexto de computação para executar `RevoScaleR` funções de seu cliente local para seu cluster HDInsight.

6. Você tem a opção de aplicar as Configurações da Ciência de Dados para o ambiente do Visual Studio, que fornece um novo layout para seu espaço de trabalho para as ferramentas de R.
    1. Para salvar suas configurações atuais do Visual Studio, use o comando **Ferramentas > Importar e exportar configurações** e, em seguida, selecione **Exportar configurações de ambiente selecionadas** e especifique um nome de arquivo. Para restaurar essas configurações, use o mesmo comando e selecione **Importar configurações de ambiente selecionadas**.

    2. Vá para o item de menu **Ferramentas R**, selecione **Configurações da Ciência de Dados...** .

        ![Configurações da Ciência de Dados...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Usando a abordagem na etapa 1, você pode também salvar e restaurar o layout de cientista de dados personalizado, em vez de repetir o comando **Configurações da Ciência de Dados**.

## <a name="execute-local-r-methods"></a>Executar métodos locais de R

1. Crie seu [cluster R Server HDInsight](r-server-get-started.md).
2. Instale a [extensão RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Faça o download do [arquivo zip de exemplos](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar a solução no Visual Studio.
5. Abra o `1-Getting Started with R.R` arquivo na `A first look at R` pasta da solução.
6. Iniciando na parte superior do arquivo, pressione Ctrl + Enter para enviar cada linha, uma de cada vez, à janela de R Interativo. Algumas linhas podem levar algum tempo já que instalam pacotes.
    * Como alternativa, você pode selecionar todas as linhas no arquivo de R (Ctrl + A), em seguida, execute todos os (Ctrl + Enter) ou selecione o ícone Executar em interativo na barra de ferramentas.
        ![Executar em Interativo](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Depois de executar todas as linhas no script, você verá uma saída semelhante a este:

    ![Configurações da Ciência de Dados...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Enviar trabalhos para um cluster R HDInsight

Usando um Microsoft R Server/Microsoft R Client de um computador com Windows equipado com PuTTY, você pode criar um contexto de computação que executará funções distribuídas `RevoScaleR` de seu cliente local para seu cluster HDInsight. Use `RxSpark` para criar o contexto de computação, especificando seu nome de usuário, nó de borda do cluster Hadoop, comutadores SSH e assim por diante.

1. Para localizar o nome do host do seu nó Edge, abra o painel de cluster R HDInsight no Azure, selecione **Secure Shell (SSH)** no menu superior do painel de visão geral.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Copie o valor **nome de host do nó Edge**.

    ![Nome do host do nó Edge](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Cole o código a seguir na janela de R Interativo no Visual Studio, alterando os valores das variáveis de programa de instalação para coincidir com seu ambiente.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![Definir o contexto do Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Execute os comandos a seguir na janela de R Interativo:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Você deverá ver um resultado semelhante ao seguinte:

    ![Execução bem-sucedida do comando de rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Verifique se o arquivo `rxHadoopCopy` foi copiados com êxito `people.json` da pasta de dados de exemplo para a `/user/RevoShare/newUser` pasta recém-criada:

    1. No painel do cluster R HDInsight no Azure, selecione **contas de armazenamento** no menu à esquerda.

        ![Contas de armazenamento](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Selecione a conta de armazenamento padrão para o cluster, anotando o nome do contêiner/diretório.

    3. Selecione **Contêineres** no menu à esquerda no painel de conta de armazenamento.

        ![Contêineres](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Selecione nome do contêiner do cluster, navegue até a pasta **Usuário** (você talvez precise clicar *Carregar mais* na parte inferior da lista), em seguida, selecione *RevoShare*, em seguida, **newUser**. O `people.json` arquivo deve ser exibido no `newUser` pasta.

        ![Copiado de](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Depois de terminar de usar o contexto atual do Spark, você deve pará-lo. Você não pode executar simultaneamente vários contextos.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Próximas etapas

* [Opções de contexto de computação para o Servidor R no HDInsight](r-server-compute-contexts.md)
* [Combinar ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo de previsões de atraso de voo aérea.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
