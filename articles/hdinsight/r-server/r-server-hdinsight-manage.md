---
title: Gerenciar o cluster do ML Services no Azure HDInsight
description: Saiba como gerenciar um cluster do ML Services no Microsoft Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: b927c1d3bef3382680552a0b25e49bd7c10b6baa
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620239"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Gerenciar cluster do ML Services no Microsoft Azure HDInsight

Neste artigo, você aprenderá como gerenciar um cluster do ML Services existente no Microsoft Azure HDInsight para executar tarefas como adicionar vários usuários simultâneos, conectar remotamente a um cluster do ML Services, alterar o contexto de computação etc.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do ML Services no HDInsight** : Para obter instruções, consulte [Introdução ao ML Services no HDInsight](r-server-get-started.md).

* **Um cliente Secure Shell (SSH)**: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Habilitar múltiplos usuário simultâneos

Você pode habilitar vários usuários simultâneos ao cluster do ML Service no HDInsight adicionando mais usuários ao nó de borda em que a versão da comunidade RStudio é executada. Quando você cria um cluster HDInsight, deve fornecer dois usuários, um usuário HTTP e um usuário SSH:

![Usuários simultâneos 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nome de usuário de logon de cluster**: um usuário HTTP para autenticação por meio do gateway de HDInsight que é usado para proteger os clusters HDInsight criados. Este usuário HTTP é usado para acessar a IU do Ambari, a IU do YARN e outros componentes de interface do usuário.
- **Nome de usuário SSH (Secure Shell)**: um usuário SSH para acessar o cluster por meio do secure shell. Esse é um usuário no sistema Linux para todos os nós de cabeçalho, nós de trabalho e nós de borda. Portanto, você pode usar o secure shell para acessar qualquer nó em um cluster remoto.

A versão da Comunidade do ML Service usada no cluster do ML Services no HDInsight aceita apenas o nome de usuário e senha do Linux como mecanismo de entrada. Ele não dá suporte a tokens de passagem. Assim, quando você tentar acessar o R Studio pela primeira vez em um cluster do ML Services você precisará entrar duas vezes.

- Primeiro, entre usando as credenciais de usuário HTTP por meio do Gateway do HDInsight. 

- Em seguida, use as credenciais de usuário do SSH para entrar no RStudio.
  
Atualmente, apenas uma conta de usuário SSH pode ser criada durante o provisionamento de um cluster HDInsight. Portanto, para permitir que vários usuários acessem o ML Services em clusters no HDInsight, precisamos criar usuários adicionais no sistema Linux.

Como o RStudio está em execução no nó de borda do cluster, há várias etapas aqui:

1. Use o usuário SSH existente para entrar no nó de borda
2. Adicionar mais usuários do Linux ao nó de borda
3. Use a versão do RStudio Community com o usuário criado

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Etapa 1: usar o usuário SSH criado para entrar no nó de borda

Em seguida, siga as instruções fornecidas em [Conectar-se ao HDInsight (Hadoop) usando o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) para acessar o nó de borda. É o endereço do nó de borda para o cluster do ML Services no HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Etapa 2: adicionar mais usuários do Linux ao nó de borda

Para adicionar um usuário ao nó de borda, execute os comandos:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

A captura de tela a seguir mostra o resultado.

![Usuários simultâneos 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Quando a Senha atual do Kerberos for solicitada, basta pressionar **Enter** para ignorar a solicitação. A opção `-m` no comando `useradd` indica que o sistema criará uma pasta base para o usuário, obrigatória para a versão do RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Etapa 3: usar a versão do RStudio Community com o usuário criado

Acessar RStudio a pártir de https://CLUSTERNAME.azurehdinsight.net/rstudio/. Se você estiver fazendo logon pela primeira vez depois de criar o cluster, insira as credenciais de administrador de cluster seguidas as credenciais de usuário SSH que você criou. Se não for o primeiro logon, apenas digite as credenciais de usuário SSH que você criou.

Você também pode entrar usando as credenciais originais (por padrão, ela é *sshuser*) simultaneamente em outra janela do navegador.

Observe também que os usuários adicionados recentemente não têm privilégios de raiz no sistema Linux, mas têm o mesmo acesso a todos os arquivos nos armazenamentos remotos HDFS e WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Conectar remotamente o Microsoft ML Services

É possível configurar o acesso ao contexto de computação do HDInsight Hadoop Spark a partir de uma instância remota do ML Client em execução na área de trabalho. Para isso, você precisará especificar as seguintes opções (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript), ao definir o contexto de computação RxSpark em seu desktop: Por exemplo:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
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

Para obter mais informações, consulte a seção "Usar o Microsoft Machine Learning Server como Cliente Hadoop" em [Como usar o RevoScaleR em um contexto de computação do Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Use um contexto de computação

Um contexto de computação permite que você controle se o cálculo é executado localmente no nó de borda ou distribuído entre os nós no cluster HDInsight.

1. No Servidor do RStudio ou no console R (em uma seção do SSH), use o seguinte código para carregar dados de exemplo no armazenamento padrão do HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. Em seguida, crie algumas informações de dados e defina duas fontes de dados.

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

3. Execute uma regressão logística com os dados usando o contexto de computação local.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Você deverá visualizar a saída que termina com linhas semelhantes ao seguinte trecho de código:

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

4. Execute a mesma regressão logística usando o contexto do Spark. O contexto do Spark distribui o processamento em todos os nós de trabalho no cluster HDInsight.

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
   > Você também pode usar o MapReduce para distribuir a computação nos nós do cluster. Para saber mais sobre o contexto de computação, confira [Opções de contexto de computação para o ML Services no HDInsight](r-server-compute-contexts.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribua o código R em vários nós

Com o ML Services no HDInsight, você pode facilmente usar o código R existente e executá-lo em vários nós no cluster usando `rxExec`. Essa função é útil ao fazer uma limpeza de parâmetro ou simulações. O código abaixo é um exemplo de como usar o `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se você ainda estiver usando o contexto do Spark ou do MapReduce, esse comando retornará o valor do nome do nó para os nós de trabalho nos quais o código `(Sys.info()["nodename"])` é executado. Por exemplo, em um cluster de quatro nós, você deve receber uma saída semelhante ao trecho de código a seguir:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-hive-and-parquet"></a>Acessar dados no Hive e no Parquet

O ML Services do HDInsight permite acesso direto aos dados no Hive e Parquet para uso pelas funções do ScaleR no contexto de computação do Spark. Esses recursos estão disponíveis por meio de novas funções de fontes de dados ScalR chamadas RxHiveData e RxParquetData, que funcionam com o uso de Spark SQL para carregar dados diretamente em um DataFrame do Spark para serem analisados pelo ScaleR.

O código abaixo fornece exemplos de código na utilização das novas funções:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Para saber mais sobre o uso dessas novas funções, confira a ajuda online no ML Services usando os comandos `?RxHivedata` e `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalar pacotes R adicionais no cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Para Instalar pacotes R adicionais no nó de borda

Se desejar instalar pacotes R adicionais no nó de borda, você poderá usar o `install.packages()` diretamente do console R quando conectado ao nó de borda por meio do SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Para instalar pacotes R no nó da função de trabalho

Para instalar pacotes R em nós de trabalho do cluster, você deverá usar uma ação de script. As Ações de Script são scripts de Bash que são usados para fazer alterações de configuração no cluster HDInsight ou para instalar um software adicional, como outros pacotes R. 

> [!IMPORTANT]
> As Ações de Script para instalar pacotes R adicionais só podem ser usadas depois que o cluster foi criado. Não use esse procedimento durante a criação do cluster, pois o script depende da configuração completa do ML Services.
>
>

1. Siga as etapas em [Personalizar clusters usando a Ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. Para **Enviar ação de script**, forneça as seguintes informações:

   * Para **Tipo de Script**, selecione **Personalizar**.

   * Para **Nome**, forneça um nome para a ação de script.

    * Para uma **URI do script Bash**, insira `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Este é o script que instala os pacotes R adicionais no nó de trabalho

   * Marque a caixa de seleção apenas para **Trabalho**.

   * **Parâmetros**: os pacotes R a serem instalados. Por exemplo, `bitops stringr arules`

   * Marque a caixa de seleção para **Manter essa ação de script**.  

   > [!NOTE]
   > 1. Por padrão, todos os pacotes R são instalados por meio de um instantâneo do repositório do Microsoft MRAN consistente com a versão do ML Server que foi instalado. Se você quiser instalar versões mais recentes dos pacotes, há alguns riscos de incompatibilidade. No entanto, esse tipo de instalação é possível especificando `useCRAN` como o primeiro elemento do pacote de lista, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes R exigem outras bibliotecas do sistema Linux. Por conveniência, o ML Services do HDInsight vem pré-instalado com as dependências necessárias para os 100 pacotes R mais populares. No entanto, se os pacotes R instalados exigirem bibliotecas além dessas, você deverá baixar o script base usado aqui e adicionar etapas para instalar as bibliotecas do sistema. Em seguida, você deverá carregar o script modificado em um contêiner de blob público no armazenamento do Azure e usar o script modificado para instalar os pacotes.
   >    Para saber mais sobre como desenvolver as Ações de Script, confira [Desenvolvimento de ação de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Adicionando uma ação do script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Escolha **Criar** para executar o script. Quando o script é concluído, os pacotes R ficam disponíveis em todos os nós de trabalho.

## <a name="next-steps"></a>Próximas etapas

* [Operacionalizar o cluster do ML Services no HDInsight](r-server-operationalize.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [Opções de Armazenamento do Microsoft Azure para cluster do ML Services no HDInsight](r-server-storage.md)
