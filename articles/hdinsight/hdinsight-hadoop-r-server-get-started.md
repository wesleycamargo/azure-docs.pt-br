---
title: Introdução ao Servidor R no HDInsight (preview) | Microsoft Docs
description: Saiba como criar um Apache Spark no cluster HDInsight (Hadoop) que inclui o Servidor R (visualização) e, então, enviar um script R no cluster.
services: HDInsight
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/19/2016
ms.author: jeffstok

---
# Introdução ao uso do Servidor R no HDInsight (preview)
A oferta da camada premium do HDInsight inclui o Servidor R como parte do cluster HDInsight (visualização). Isso permite que os scripts R usem o MapReduce e o Spark para executar cálculos distribuídos. Neste documento, você aprenderá como criar um novo Servidor R no HDInsight e, então, executar um script R que demonstre o uso do Spark para cálculos R distribuídos.

![Diagrama de fluxo de trabalho para este documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Pré-requisitos
* **Uma assinatura do Azure**: antes de começar este tutorial, você deve ter uma assinatura do Azure. Confira a seção [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) para obter mais informações.
* **Um cliente Secure Shell (SSH)**: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Os sistemas Linux, Unix e OS X fornecem um cliente SSH por meio do comando `ssh`. Para sistemas Windows, é recomendável [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
  
  * **Chaves SSH (opcional)**: você pode proteger a conta SSH usada para se conectar ao cluster usando uma senha ou uma chave pública. Usar uma senha é mais fácil e permite que você comece sem precisar criar um par de chaves pública/privada; No entanto, usar uma chave é mais seguro.
    
      As etapas neste documento pressupõem que você esteja usando uma senha. Para obter informações sobre como criar e usar chaves SSH com o HDInsight, consulte os seguintes documentos:
    
    * [Usar SSH com HDInsight no Linux, Unix ou em clientes OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usar SSH com o HDInsight em clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Criar o cluster
> [!NOTE]
> As etapas neste documento criam um Servidor R no HDInsight usando as informações de configuração básica. Para outras definições de configuração do cluster (por exemplo, adicionar contas de armazenamento extras, usar uma Rede Virtual do Azure ou criar um metastore para o Hive), confira a seção [Create Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters HDInsight baseados em Linux).
> 
> 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **NOVO**, **Dados + Análise** e, então, **HDInsight**.
   
    ![Imagem da criação de um novo cluster](./media/hdinsight-getting-started-with-r/newcluster.png)
3. Insira um nome para o cluster no campo **Nome do Cluster**. Se você tiver várias assinaturas do Azure, use a entrada **Assinatura** para selecionar aquela que deseja usar.
   
    ![Nome do cluster e seleções de assinatura](./media/hdinsight-getting-started-with-r/clustername.png)
4. Escolha a opção **Selecionar tipo de Cluster**. Na folha **Tipo de Cluster**, selecione as seguintes opções:
   
   * **Tipo de Cluster**: Servidor R no Spark
   * **Camada do Cluster**: Premium
     
     Deixe as outras opções com os valores padrão e, então, use o botão **Selecionar** para salvar o tipo de cluster.
     
     ![Captura de tela do tipo de folha do cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
     
     > [!NOTE]
     > Você também pode adicionar o Servidor R em outros tipos de cluster HDInsight (como Hadoop ou HBase,) selecionando o tipo de cluster e, então, **Premium**.
     > 
     > 
5. Selecione **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um no qual o cluster será criado. Ou então, você pode selecionar **Criar Novo** e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar que o novo nome de grupo está disponível.
   
   > [!NOTE]
   > A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.
   > 
   > 
   
    Use o botão **Selecionar** para salvar o grupo de recursos.
6. Selecione **Credenciais**, em seguida, digite um **Nome de usuário de logon de cluster** e **Senha de logon do Cluster**.
   
    Insira um **nome de usuário SSH**. O SSH é usado para se conectar remotamente ao cluster usando um cliente **SSH (Secure Shell)**. Você pode especificar o usuário SSH nesta caixa de diálogo ou depois que o cluster foi criado (guia de configuração do cluster). O Servidor R está configurado para esperar um **nome de usuário SSH** de "remoteuser". Se você usar um nome de usuário diferente, precisará executar uma etapa adicional após a criação do cluster.
   
    ![Folha Credenciais](./media/hdinsight-getting-started-with-r/clustercredentials.png)
   
    **Tipo de autenticação SSH**: selecione **PASSWORD** como o tipo de autenticação, a menos que você preferir o uso de uma chave pública. Você precisará de um par de chaves pública/privada se quiser acessar o Servidor R no cluster por meio de um cliente remoto, por exemplo, RTVS, RStudio ou outra área de trabalho IDE.
   
    Para criar e usar um par de chaves pública/privada, selecione “PUBLIC KEY” e faça o seguinte. Estas instruções pressupõem que você tenha o Cygwin com ssh-keygen ou equivalente instalado.
   
   * Gere um par de chaves pública/privada do prompt de comando em seu laptop:
     
        ssh-keygen -t rsa -b 2048 –f <private-key-filename>
   * Isso criará um arquivo de chave privada e um arquivo de chave pública com o nome <private-key-filename>.pub, por exemplo, davec e davec.pub. Em seguida, especifique o arquivo de chave pública (*. pub) ao atribuir credenciais de cluster do HDI:
     
     ![Folha Credenciais](./media/hdinsight-getting-started-with-r/publickeyfile.png)
   * Alterar permissões em keyfile particular em seu laptop
     
        chmod 600 <private-key-filename>
   * Usar o arquivo de chave privada com o SSH para logon remoto, por exemplo
     
        ssh –i <private-key-filename> remoteuser@<hostname public ip>
     
     ou como parte da definição de seu contexto de computação Hadoop Spark para Servidor R no cliente (consulte Using Microsoft R Server as a Hadoop Client (Usando o Microsoft R Server como um cliente Hadoop) na seção [Creating a Compute Context for Spark (Criando um contexto de computação para o Spark)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) do guia online [RevoScaleR Hadoop Spark Getting Started (Introdução ao RevoScaleR no Hadoop Spark)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).
7. Selecione a opção **Fonte de Dados** para escolher uma fonte de dados para o cluster. Selecione uma conta de armazenamento existente ao escolher a opção **Selecionar conta de armazenamento** e, em seguida, selecionando a conta, ou crie uma nova conta usando o link **Novo** na seção **Selecionar conta de armazenamento**.
   
    Se escolher **Novo**, você deverá digitar um nome para a nova conta de armazenamento. Uma marca de seleção verde será exibida se o nome for aceito.
   
    O **Contêiner Padrão** usará o nome do cluster como padrão. Deixe-o como o valor.
   
    Selecione **Localização** para escolher a região na qual criar a conta de armazenamento.
   
   > [!IMPORTANT]
   > Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.
   > 
   > 
   
    Use o botão **Selecionar** para salvar a configuração da fonte de dados.
   
    ![Folha de fonte de dados](./media/hdinsight-getting-started-with-r/datastore.png)
8. Selecione **Camadas de Preços de Nó** para exibir informações sobre os nós que serão criados para esse cluster. A menos que você saiba que precisará de um cluster maior, deixe o número de nós de trabalho com o padrão de `4`. O custo estimado do cluster será mostrado na folha.
   
   > [!NOTE]
   > Se necessário, redimensione o cluster posteriormente por meio do Portal (Cluster -> Configurações -> Dimensionar Cluster) para aumentar ou diminuir o número de nós de trabalho. Isso pode ser útil para deixar o cluster ocioso quando não estiver em uso ou para adicionar capacidade para atender às necessidades das tarefas maiores.
   > 
   > 
   
    Alguns fatores para ter em mente ao dimensionar o cluster, os nós de dados e o nó de borda incluem:
   
   * O desempenho da análise do Servidor R distribuída no Spark é proporcional ao número de nós de trabalho quando os dados são grandes.
   * O desempenho da análise do Servidor R é linear ao tamanho dos dados que estão sendo analisados. Por exemplo:
     
     * Para os tamanhos de dados pequenos e médios, o desempenho será melhor quando analisados em um contexto de computação local no nó de borda. Para saber mais sobre os cenários nos quais os contextos de computação local e Spark funcionam melhor, veja as opções de contexto de computação para Servidor R no HDInsight.<br>
     * Se você fizer logon no nó de borda e executar seu script R, todas as funções, exceto as funções rx ScaleR, serão executadas <strong>localmente</strong> no nó de borda, de modo que a memória e o número de núcleos do nó de borda deverão ser dimensionados adequadamente. O mesmo se aplica se você usar o Servidor R no HDI como contexto de computação remota do seu laptop.
     
     ![Folha de camadas de preços de nó](./media/hdinsight-getting-started-with-r/pricingtier.png)
     
     Use o botão **Selecionar** para salvar a configuração de preços do nó.
9. Na folha **Novo Cluster HDInsight**, verifique se a opção **Fixar no Quadro Inicial** está marcada e selecione **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está sendo criado e que será alterado para exibir o ícone do HDInsight após a conclusão da criação.
   
   | Durante a criação | Criação concluída |
   | --- | --- |
   | ![Criando um indicador no quadro inicial](./media/hdinsight-getting-started-with-r/provisioning.png) |![Bloco de cluster criado](./media/hdinsight-getting-started-with-r/provisioned.png) |
   
   > [!NOTE]
   > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de criação.
   > 
   > 

## Conecte-se ao nó de borda do Servidor R
Conecte-se ao nó de borda do Servidor R do cluster HDInsight usando o SSH:

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net

> [!NOTE]
> Você também pode encontrar o endereço do `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` no portal do Azure ao selecionar seu cluster e, em seguida, **Todas as Configurações**, **Aplicativos** e **RServer**. Isso exibirá as informações do ponto de extremidade do SSH para o nó de borda.
> 
> ![Imagem do ponto de extremidade do SSH para o nó de borda](./media/hdinsight-getting-started-with-r/sshendpoint.png)
> 
> 

Caso você tenha usado uma senha para proteger sua conta de usuário SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`.

Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:

* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Uma vez conectado, você chegará em um prompt semelhante ao seguinte.

    username@ed00-myrser:~$

## Use o console R
1. Na sessão de SSH, use o seguinte comando para iniciar o console R.
   
        R
   
    Você verá saídas semelhantes ao seguinte.
   
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)
   
        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.
   
        Natural language support but running in an English locale
   
        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.
   
        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.
   
        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation
   
        Type 'readme()' for release notes.
   
        >
2. No prompt `>`, você pode inserir o código R. O Servidor R inclui pacotes que permitem que você interaja com o Hadoop e execute cálculos distribuídos com facilidade. Por exemplo, use o seguinte comando para exibir a raiz do sistema de arquivos padrão para o cluster HDInsight.
   
        rxHadoopListFiles("/")
   
    Você também pode usar o endereçamento de estilo WASB.
   
        rxHadoopListFiles("wasbs:///")

## Usando o Servidor R no HDI de uma instância remota do Microsoft R Server ou Microsoft R Client
Na seção acima sobre uso de pares de chaves pública/privada para acessar o cluster, é possível configurar o acesso ao contexto de computação do HDI Hadoop Spark de uma instância remota do Microsoft R Server ou do Microsoft R Client em execução em um desktop ou um laptop (veja Using Microsoft R Server as a Hadoop Client (Usando o Microsoft R Server como um cliente Hadoop) na seção [Creating a Compute Context for Spark (Criando um contexto de computação para o Spartk)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) do guia online [RevoScaleR Hadoop Spark Getting Started (Introdução ao RevoScaleR no Hadoop Spark)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)). Para isso, você precisará especificar as seguintes opções ao definir o contexto de computação RxSpark em seu laptop: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Por exemplo:

    myNameNode <- "default"
    myPort <- 0 

    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )



## Use um contexto de computação
Um contexto de computação permite que você controle se o cálculo será executado localmente no nó de borda, ou se ele será distribuído entre os nós no cluster HDInsight.

1. No console R, use o seguinte para carregar dados de exemplo no armazenamento padrão do HDInsight.
   
        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)
2. Em seguida, criaremos algumas informações de dados e definiremos duas fontes de dados para que possamos trabalhar com os dados.
   
        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))
   
        # get all the column names
        varNames <- names(airlineColInfo)
   
        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
   
        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
3. Vamos executar uma regressão logística com os dados usando o contexto de computação local.
   
        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)
   
    Você deve ver a saída que termina com linhas semelhantes à seguinte.
   
        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
   
        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
   
        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7
4. Em seguida, vamos executar a mesma regressão logística usando o contexto do Spark. O contexto do Spark distribuirá o processamento em todos os nós de trabalho no cluster HDInsight.
   
        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)
   
   > [!NOTE]
   > Você também pode usar o MapReduce para distribuir a computação nos nós do cluster. Para saber mais sobre o contexto de computação, confira [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).
   > 
   > 

## Distribua o código R em vários nós
Com o Servidor R, você pode facilmente usar o código R existente e executá-lo em vários nós no cluster usando o `rxExec`. Isso é útil ao fazer uma limpeza de parâmetro ou simulações. Veja a seguir um exemplo de como usar o `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se você ainda estiver usando o contexto do Spark ou do MapReduce, o valor do nome do nó será retornado para os nós de trabalho nos quais o código (`Sys.info()["nodename"]`) é executado. Por exemplo, em um cluster de quatro nós, você pode receber a saída semelhante à seguinte.

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

## Instalar pacotes R
Se desejar instalar pacotes R adicionais no nó de borda, você poderá usar o `install.packages()` diretamente do console R quando conectado ao nó de borda por meio do SSH. No entanto, se você precisar instalar pacotes R em nós de trabalho do cluster, você deverá usar uma Ação de Script.

As Ações de Script são scripts de Bash que são usados para fazer alterações de configuração no cluster HDInsight ou para instalar um software adicional. Nesse caso, para instalar pacotes R adicionais. Para instalar pacotes adicionais usando uma Ação de Script, siga as etapas a seguir.

> [!IMPORTANT]
> As Ações de Script para instalar pacotes R adicionais só podem ser usadas depois que o cluster foi criado. Ele não deve ser usado durante a criação do cluster, uma vez que o script precisa que o Servidor R esteja completamente instalado e configurado.
> 
> 

1. No [Portal do Azure](https://portal.azure.com), escolha o Servidor R no cluster HDInsight.
2. Na folha do cluster, escolha **Todas as Configurações** e **Ações de Script**. Na folha **Ações de Script**, escolha **Enviar Novo** para enviar uma nova Ação de Script.
   
    ![Imagem da folha de ações do script](./media/hdinsight-getting-started-with-r/newscriptaction.png)
3. Na folha **Enviar ação de script**, forneça as informações a seguir.
   
   * **Nome**: um nome amigável para identificar esse script
   * **URI do script Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
   * **Cabeçalho**: deve estar **desmarcado**
   * **Trabalho**: deve estar **marcado**
   * **Zookeeper**: deve estar **desmarcado**
   * **Parâmetros**: os pacotes R a serem instalados. Por exemplo, `bitops stringr arules`
   * **Persistir esse script...**: deve estar **marcado**
     
     > [!NOTE]
     > 1. Por padrão, todos os pacotes R são instalados por meio de um instantâneo do repositório do Microsoft MRAN consistente com a versão do R Server que foi instalado. Se você quiser instalar versões mais recentes dos pacotes, haverá um risco de incompatibilidade. Porém, isso é possível com a especificação de `useCRAN` como o primeiro elemento do pacote de lista, por exemplo, `useCRAN bitops, stringr, arules`.
     > 2. Alguns pacotes R exigirão outras bibliotecas do sistema do Linux. Para sua conveniência, pré-instalamos as dependências necessárias aos 100 pacotes R mais populares. No entanto, se os pacotes R que você instalar exigirem bibliotecas além dessas, você deverá baixar o script base usado aqui e adicionar etapas para instalar as bibliotecas do sistema. Em seguida, você deverá carregar o script modificado em um contêiner de blob público no armazenamento do Azure e usar o script modificado para instalar os pacotes. Para saber mais sobre como desenvolver as Ações de Script, confira [Desenvolvimento de ação de script](hdinsight-hadoop-script-actions-linux.md).
     > 
     > 
     
     ![Adicionando uma ação do script](./media/hdinsight-getting-started-with-r/scriptaction.png)
4. Escolha **Criar** para executar o script. Quando o script for concluído, os pacotes R estarão disponíveis em todos os nós de trabalho.

## Próximas etapas
Agora que você entende como criar um novo cluster HDInsight que inclui o Servidor R e as noções básicas sobre como usar o console R em uma sessão SSH, use o seguinte para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

* [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de contexto de computação para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opções de armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

### Modelos do Gerenciador de Recursos do Azure
Se você estiver interessado em automatizar a criação de um Servidor R no HDInsight usando modelos do Azure Resource Manager, confira os seguintes modelos de exemplo.

* [Crie um Servidor R no cluster HDInsight usando uma chave pública do SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Crie um Servidor R no cluster HDInsight usando uma senha do SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Ambos os modelos criam um novo cluster HDInsight e uma conta de armazenamento associada e podem ser usados no CLI do Azure, no Azure PowerShell ou no Portal do Azure.

Para obter informações genéricas sobre como usar os modelos do Azure Resource Manager, confira [Criar clusters Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0921_2016-->