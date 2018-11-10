---
title: Introdução aos Serviços de ML no HDInsight – Azure
description: Saiba como criar um Apache Spark no cluster HDInsight que inclui ML Services e enviar um script R no cluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 0811f0e115545a37b6c570c436900a8548adae61
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006355"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Introdução ML Services no Azure HDInsight

O Azure HDInsight permite criar um cluster do ML Services. Essa opção permite que scripts R usem o Spark e MapReduce para executar cálculos distribuídos. Neste artigo, você aprende a criar um cluster do ML Services do HDInsight e como executar um script R que demonstra o uso do Spark para cálculos R distribuídos.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**: antes de começar este tutorial, você deve ter uma assinatura do Azure. Para obter mais informações, consulte [Obter uma avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cliente Secure Shell (SSH)**: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Como criar o cluster usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Clique em **Criar um recurso** > **Dados + Análise** > **HDInsight**.

3. Em **Noções Básicas**, insira as seguintes informações:

    * **Nome do cluster**: o nome do cluster HDInsight.
    * **Assinatura**: selecione a assinatura a ser utilizada.
    * **Nome de usuário de logon do cluster** e **Senha de logon do cluster**: logon ao acessar o cluster por HTTPS. Você pode usar essas credenciais para acessar serviços como a interface do usuário da Web do Ambari ou a API REST.
    * **Nome de usuário do SSH (Secure Shell)**: o logon usado ao acessar o cluster via SSH. Por padrão, a senha é a mesma do logon do cluster.
    * **Grupo de Recursos**: o grupo de recursos para criar o cluster.
    * **Local**: a região do Azure para criar o cluster.

        ![Detalhes básicos do cluster](./media/r-server-get-started/clustername.png)

4. Selecione **Tipo de cluster** e defina os seguintes valores na seção **Configuração de Cluster**:

    * **Tipo de cluster**: ML Services

    * **Sistema Operacional**: Linux

    * **Versão**: ML Server 9.3 (HDI 3.6). As notas de versão do ML Server 9.3 estão disponíveis em [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **Edição de comunidade R Studio para ML Server**: Esse IDE baseado em navegador é instalado por padrão no nó de borda. Desmarque a caixa de seleção se preferir não o instalar. Se optar por instalá-lo, a URL para acessar o logon do servidor do RStudio está disponível na folha de aplicativo do portal para o cluster depois de ele ter sido criado.

        ![Detalhes básicos do cluster](./media/r-server-get-started/clustertypeconfig.png)

4. Depois de selecionar o tipo de cluster, use o botão __Selecionar__ para definir o tipo de cluster. Em seguida, use o botão __Avançar__ para concluir a configuração básica.

5. Na seção **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para as etapas neste documento, deixe os outros campos nessa janela com os valores padrão. Use o botão __Avançar__ para salvar a configuração de armazenamento.

    ![Definir as configurações de conta de armazenamento do HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Na seção **Resumo**, examine a configuração do cluster. Use os links __Editar__ para alterar as configurações que estão incorretas. Por fim, use o botão __Criar__ para criar o cluster.

    ![Definir as configurações de conta de armazenamento do HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Pode levar até 20 minutos para criar o cluster.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Conectar-se ao RStudio Server

Se optou por instalar o RStudio Server Community Edition como parte do seu cluster HDInsight, você pode acessar o logon do RStudio usando um dos dois métodos a seguir:

* **Opção 1** – Vá até a URL a seguir (onde **CLUSTERNAME** é o nome do cluster do ML Services que você criou):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opção 2** – Abra o cluster do ML Services no Portal do Azure e, em **Links rápido**, clique em **Painéis do ML Services**.

     ![Definir as configurações de conta de armazenamento do HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    Em **Painéis do Cluster**, clique em **R Studio Server**.

    ![Definir as configurações de conta de armazenamento do HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Não importa o método usado: na primeira vez em que você fizer logon, precisará autenticar duas vezes.  No primeiro prompt de autenticação, forneça o *userid de administrador do cluster* e a *senha*. No segundo prompt de autenticação, forneça a *ID de usuário do SSH* e a *senha*. Os logons subsequentes exigirão apenas as credenciais do SSH.

Quando você estiver conectado, sua tela deve ser semelhante à captura de tela a seguir:

![Conectar-se ao RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Executar um trabalho de exemplo

Você pode enviar um trabalho usando funções ScaleR. Aqui está um exemplo dos comandos usados para executar um trabalho:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Conecte-se ao nó de borda do cluster

Nesta seção, você saberá como se conectar ao nó de borda de um cluster HDInsight ML Services usando o SSH. Para se familiarizar sobre como usar o SSH, consulte [Usar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

O comando SSH para se conectar ao nó de borda do cluster do ML Services é:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Para encontrar o comando SSH para seu cluster no Portal do Azure, clique no nome do cluster, depois em **SSH + Logon no cluster** e, em seguida, para **Hostname**, selecione o nó de borda. Isso exibe as informações do ponto de extremidade do SSH para o nó de borda.

![Imagem do ponto de extremidade do SSH para o nó de borda](./media/r-server-get-started/sshendpoint.png)

Caso você tenha usado uma senha para proteger sua conta de usuário do SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Uma vez conectado, você obtém um prompt semelhante ao seguinte:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Use o console R

1. Na sessão de SSH, use o seguinte comando para iniciar o console R:  

        R

2. Você deve ver uma saída com a versão do ML Server, além de outras informações.
    
3. No prompt `>` , você pode inserir o código R. O ML Services no HDInsight incluem pacotes que permitem interagir com o Hadoop e executar cálculos distribuídos com facilidade. Por exemplo, use o seguinte comando para exibir a raiz do sistema de arquivos padrão para o cluster HDInsight:

        rxHadoopListFiles("/")

4. Você também pode usar o endereçamento de estilo WASB.

        rxHadoopListFiles("wasb:///")

5. Para fechar o console R, use o seguinte comando:

        quit()

## <a name="automated-cluster-creation"></a>Criação de cluster automatizada

Você pode automatizar a criação do cluster do ML Services para HDInsight usando o SDK e o PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Para criar um cluster do ML Services usando o SDK do .NET, consulte [Criar clusters baseados em Linux no HDInsight usando o SDK do .NET.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Para criar um cluster do ML Services usando o powershell, consulte o artigo sobre [Criar clusters do HDInsight usando o Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar um novo cluster do ML Services no Azure HDInsight e os fundamentos de como usar o console do R em uma sessão do SSH. Os artigos a seguir explicam outras maneiras de gerenciar e trabalhar com o ML Services no HDInsight:

* [Enviar trabalhos a partir das Ferramentas do R para Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Gerenciar o cluster do ML Services no HDInsight](r-server-hdinsight-manage.md)
* [Operacionalizar o cluster do ML Services no HDInsight](r-server-operationalize.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [Opções de Armazenamento do Microsoft Azure para cluster do ML Services no HDInsight](r-server-storage.md)
