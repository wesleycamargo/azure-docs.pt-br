<properties
   pageTitle="Introdução ao Servidor R no HDInsight (preview) | Azure"
   description="Saiba como criar um Apache Spark no cluster HDInsight (preview) que inclui o Servidor R e enviar um script R no cluster em seguida."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/25/2016"
   ms.author="jeffstok"/>

#Introdução ao uso do Servidor R no HDInsight (preview)

A oferta da camada premium para o HDInsight inclui o Servidor R no HDInsight (preview). Isso permite que os scripts R usem o MapReduce e o Spark para executar cálculos distribuídos. Neste documento, você aprenderá como criar um novo Servidor R no HDInsight e, então, executar um script R que demonstre o uso do Spark para cálculos R distribuídos.

![Diagrama de fluxo de trabalho para este documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Pré-requisitos

* __Uma assinatura do Azure__: antes de começar este tutorial, você deve ter uma assinatura do Azure. Confira a seção [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) para obter mais informações.

* __Um cliente Secure Shell (SSH)__: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Os sistemas Linux, Unix e OS X fornecem um cliente SSH por meio do comando `ssh`. Para sistemas Windows, é recomendável [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Chaves SSH (opcional)__: você pode proteger a conta SSH usada para se conectar ao cluster usando uma senha ou uma chave pública. Usar uma senha é mais fácil e permite que você comece sem precisar criar um par de chaves pública/privada; No entanto, usar uma chave é mais seguro.
    
        As etapas neste documento pressupõem que você esteja usando uma senha. Para obter informações sobre como criar e usar chaves SSH com o HDInsight, consulte os seguintes documentos:
        
        * [Usar SSH com HDInsight no Linux, Unix ou em clientes OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Usar SSH com o HDInsight em clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Criar o cluster

> [AZURE.NOTE] As etapas neste documento criam um Servidor R no HDInsight usando as informações de configuração básica. Para outras definições de configuração do cluster (por exemplo, adicionar contas de armazenamento extras, usar uma Rede Virtual do Azure ou criar um metastore para o Hive), confira a seção [Create Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters HDInsight baseados em Linux).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione __NOVO__, __Dados + Análise__ e, então, __HDInsight__.

    ![Imagem da criação de um novo cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Insira um nome para o cluster no campo __Nome do Cluster__. Se você tiver várias assinaturas do Azure, use a entrada __Assinatura__ para selecionar aquela que deseja usar.

    ![Nome do cluster e seleções de assinatura](./media/hdinsight-getting-started-with-r/clustername.png)

4. Escolha a opção __Selecionar tipo de Cluster__. Na folha __Tipo de Cluster__, selecione as seguintes opções:

    * __Tipo de Cluster__: Servidor R no Spark
    
    * __Camada do Cluster__: Premium

    Deixe as outras opções com os valores padrão e, então, use o botão __Selecionar__ para salvar o tipo de cluster.
    
    ![Captura de tela do tipo de folha do cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] Você também pode adicionar o Servidor R em outros tipos de cluster HDInsight (como Hadoop ou HBase,) selecionando o tipo de cluster e, então, __Premium__.

5. Selecione **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um no qual o cluster será criado. Ou então, você pode selecionar **Criar Novo** e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar que o novo nome de grupo está disponível.

    > [AZURE.NOTE] A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.
    
    Use o botão __Selecionar__ para salvar o grupo de recursos.

6. Selecione **Credenciais**, em seguida, digite um **Nome de usuário de logon de cluster** e **Senha de logon do Cluster**.

    Insira um __Nome de usuário SSH__ e selecione __Senha__ e, então, insira a __Senha SSH__ para configurar a conta SSH. O SSH é usado para se conectar remotamente ao cluster usando um cliente Secure Shell (SSH).
    
    Use o botão __Selecionar__ para salvar as credenciais.
    
    ![Folha Credenciais](./media/hdinsight-getting-started-with-r/clustercredentials.png)

7. Selecione a opção **Fonte de dados** para escolher uma fonte de dados para o cluster. Selecione uma conta de armazenamento existente ao escolher a opção __Selecionar conta de armazenamento__ e, em seguida, selecionando a conta, ou crie uma nova conta usando o link __Novo__ na seção __Selecionar conta de armazenamento__.

    Se você selecionar __Novo__, você deverá digitar um nome para a nova conta de armazenamento. Uma marca de seleção verde será exibida se o nome for aceito.

    O __Contêiner Padrão__ usará o nome do cluster como padrão. Deixe-o como o valor.
    
    Selecione __Local__ para escolher a região para criar a conta de armazenamento.
    
    > [AZURE.IMPORTANT] Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.

    Use o botão **Selecionar** para salvar a configuração da fonte de dados.
    
    ![Folha de fonte de dados](./media/hdinsight-getting-started-with-r/datastore.png)

8. Selecione **Camadas de Preços de Nó** para exibir informações sobre os nós que serão criados para esse cluster. A menos que você saiba que precisará de um cluster maior, deixe o número de nós de trabalho com o padrão de `4`. O custo estimado do cluster será mostrado na folha.

    ![Folha de camadas de preços de nó](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Use o botão **Selecionar** para salvar a configuração de preços do nó.
    
9. Na folha **Novo Cluster HDInsight**, certifique-se de que a opção **Fixar no Quadro Inicial** está marcada e selecione **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está sendo criado e que será alterado para exibir o ícone do HDInsight após a conclusão da criação.

    | Durante a criação | Criação concluída |
    | ------------------ | --------------------- |
    | ![Criando um indicador no quadro inicial](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Bloco de cluster criado](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de criação.

## Conecte-se ao nó de borda do Servidor R

Conecte-se ao nó de borda do Servidor R do cluster HDInsight usando o SSH:

    ssh USERNAME@rserver.CLUSTERNAME.ssh.azurehdinsight.net
    
> [AZURE.NOTE] Você também pode encontrar o endereço do `RServer.CLUSTERNAME.ssh.azurehdinsight.net` no Portal do Azure ao selecionar seu cluster e, em seguida, __Todas as Configurações__, __Aplicativos__ e __RServer__. Isso exibirá as informações do ponto de extremidade do SSH para o nó de borda.
>
> ![Imagem do ponto de extremidade do SSH para o nó de borda](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Caso você tenha usado uma senha para proteger sua conta de usuário SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@RServer.CLUSTERNAME.ssh.azurehdinsight.net`.
    
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

2. Do prompt `>`, você pode inserir o código R. O Servidor R inclui pacotes que permitem que você interaja com o Hadoop e execute cálculos distribuídos com facilidade. Por exemplo, use o seguinte comando para exibir a raiz do sistema de arquivos padrão para o cluster HDInsight.

        rxHadoopListFiles("/")
    
    Você também pode usar o endereçamento de estilo WASB.
    
        rxHadoopListFiles("wasb:///")

##Use um contexto de computação

Um contexto de computação permite que você controle se o cálculo será executado localmente no nó de borda, ou se ele será distribuído entre os nós no cluster HDInsight.
        
1. No console R, use o seguinte para carregar dados de exemplo no armazenamento padrão do HDInsight.

        # Set the NameNode and port for the cluster
        myNameNode <- "default"
        myPort <- 0
        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # Source for the data to load
        source <- system.file("SampleData/AirlineDemoSmall.csv", package="RevoScaleR")
        # Directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirlineDemoSmall") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, inputDir)

2. Em seguida, criaremos alguns fatores e definiremos uma fonte de dados para que possamos trabalhar com os dados.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, 
                                   port=myPort)
        # Create Factors for the days of the week
        colInfo <- list(DayOfWeek = list(type = "factor",
             levels = c("Monday", 
                        "Tuesday", 
                        "Wednesday", 
                        "Thursday", 
                        "Friday", 
                        "Saturday", 
                        "Sunday")))
        # Define the data source
        airDS <- RxTextData(file = inputDir, 
                            missingValueString = "M",
                            colInfo  = colInfo, 
                            fileSystem = hdfsFS)

3. Vamos executar uma regressão linear com os dados usando o contexto de computação local.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a linear regression
        system.time(
            modelLocal <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek,
                                   data = airDS)
        )
        # Display a summary 
        summary(modelLocal) 

    Você deve ver a saída que termina com linhas semelhantes à seguinte.
    
        Residual standard error: 40.39 on 582620 degrees of freedom
        Multiple R-squared: 0.01465
        Adjusted R-squared: 0.01464
        F-statistic:  1238 on 7 and 582620 DF,  p-value: < 2.2e-16
        Condition number: 10.6542

4. Em seguida, vamos executar a mesma regressão linear usando o contexto do Spark. O contexto do Spark distribuirá o processamento em todos os nós de trabalho no cluster HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark(consoleOutput=TRUE) 
        # Set the compute context 
        rxSetComputeContext(mySparkCluster) 
        # Run a linear regression 
        system.time(  
            modelSpark <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS) 
        )
        # Display a summary
        summary(modelSpark)

    A saída do processamento do Spark é gravada no console porque definimos o `consoleOutput=TRUE`.
    
    > [AZURE.NOTE] Você também pode usar o MapReduce para distribuir a computação nos nós do cluster. Para obter mais informações sobre o contexto de computação, confira a seção [Compute context options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md) (Opções de contexto de computação para o Servidor R no HDInsight Premium).

##Distribua o código R em vários nós

Com o Servidor R, você pode facilmente usar o código R existente e executá-lo em vários nós no cluster usando o `rxExec`. Isso é útil ao fazer uma limpeza de parâmetro ou simulações. A seguir há um exemplo de como usar o `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Se você ainda estiver usando o contexto do Spark ou do MapReduce, o valor do nodename será retornado para os nós de trabalho nos quais o código (`Sys.info()["nodename"]`) está sendo executado. Por exemplo, em um cluster de quatro nós, você pode receber a saída semelhante à seguinte.

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

##Instalar pacotes R

Se você deseja instalar pacotes R adicionais no nó de borda, você pode usar o `install.packages()` diretamente do console R quando conectado ao nó de borda por meio do SSH. No entanto, se você precisar instalar pacotes R em nós de trabalho do cluster, você deverá usar uma Ação de Script.

As Ações de Script são scripts de Bash que são usados para fazer alterações de configuração no cluster HDInsight ou para instalar um software adicional. Nesse caso, para instalar pacotes R adicionais. Para instalar pacotes adicionais usando uma Ação de Script, siga as etapas a seguir.

> [AZURE.IMPORTANT] As Ações de Script para instalar pacotes R adicionais só podem ser usadas depois que o cluster foi criado. Ele não deve ser usado durante a criação do cluster, uma vez que o script precisa que o Servidor R esteja completamente instalado e configurado.

1. No [Portal do Azure](https://portal.azure.com), selecione o Servidor R no cluster HDInsight.

2. Na folha do cluster, selecione __Todas as Configurações__ e, então, __Ações de Script__. Da folha __Ações de Script__, selecione __Enviar Novo__ para enviar uma nova Ação de Script.

    ![Imagem da folha de ações do script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Da folha __Enviar ação de script__, forneça as seguintes informações.

    * __Nome__: um nome amigável usado para identificar este script
    * __URI do script Bash__: http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh
    * __Cabeçalho__: deve estar __desmarcado__
    * __Trabalho__: deve estar __marcado__
    * __Zookeeper__: deve estar __desmarcado__
    * __Parâmetros__: os pacotes R a serem instalados. Por exemplo, `bitops stringr arules`
    * __Manter esse script...__: deve estar __marcado__
    
    > [AZURE.IMPORTANT] Se os pacotes R que você instalar exigirem que as bibliotecas do sistema sejam adicionadas, você deverá baixar o script base usado aqui e adicionar as etapas para instalar as bibliotecas do sistema. Em seguida, você deverá carregar o script modificado em um contêiner de blob público no armazenamento do Azure e usar o script modificado para instalar os pacotes.
    >
    >Para obter mais informações sobre como desenvolver as Ações de Script, confira a seção [Desenvolvimento de Ação de Script](hdinsight-hadoop-script-actions-linux.md).
    
    ![Adicionando uma ação do script](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Selecione __Criar__ para executar o script. Quando o script for concluído, os pacotes R estarão disponíveis em todos os nós de trabalho.
    
## Próximas etapas

Agora que você entende como criar um novo cluster HDInsight que inclui o Servidor R e as noções básicas sobre como usar o console R em uma sessão SSH, use o seguinte para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

- [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opções de contexto de computação para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opções de armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

### Modelos do Gerenciador de Recursos do Azure

Se você estiver interessado em automatizar a criação de um Servidor R no HDInsight usando modelos do Azure Resource Manager, confira os seguintes modelos de exemplo.

* [Crie um Servidor R no cluster HDInsight usando uma chave pública do SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Crie um Servidor R no cluster HDInsight usando uma senha do SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Ambos os modelos criam um novo cluster HDInsight e uma conta de armazenamento associada e podem ser usados no CLI do Azure, no Azure PowerShell ou no Portal do Azure.

Para obter informações genéricas sobre como usar os modelos ARM, confira a seção [Criar clusters Hadoop baseados em Linux no HDInsight usando modelos do ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0420_2016-->