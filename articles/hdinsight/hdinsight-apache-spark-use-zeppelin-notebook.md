---
title: "Usar ação de script para instalar notebooks Zeppelin para o cluster Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo a passo sobre como instalar e usar notebooks Zeppelin com clusters Spark no HDInsight Linux."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9e6e0a696ba27c7758c21fa46754a8539ae2255b
ms.lasthandoff: 03/29/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>Instalar notebooks Zeppelin para o cluster Apache Spark no HDInsight

Saiba como instalar notebooks Zeppelin em clusters Apache Spark e como usar esses notebooks para executar trabalhos do Spark.

> [!IMPORTANT]
> Caso tenha provisionado um cluster Spark 1.6 no HDInsight 3.5, você poderá acessar o Zeppelin por padrão usando as instruções em [Usar notebooks Zeppelin com cluster Apache Spark no Linux HDInsight](hdinsight-apache-spark-zeppelin-notebook.md). Se quiser usar o Zeppelin no cluster HDInsight, versões 3.3 ou 3.4, você deverá seguir as instruções neste artigo para instalar o Zeppelin.
>
> Não há suporte ao uso de scripts neste artigo para instalar o Zeppelin em clusters Spark 2.0.

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa ter uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="install-zeppelin-on-a-spark-cluster"></a>Instalar Zeppelin em um cluster do Spark
Você pode instalar o Zeppelin em um cluster Spark usando ação de script. A ação de script usa scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Você pode usar o script personalizado para instalar o Zeppelin do Portal do Azure, usando o SDK do .NET do HDInsight ou usando o Azure PowerShell. Você pode usar o script para instalar o Zeppelin como parte da criação do cluster ou depois que o cluster estiver em funcionamento. Os links nas seções a seguir fornecem as instruções sobre como fazer isso.

### <a name="using-the-azure-portal"></a>Usando o Portal do Azure
Para obter instruções sobre como usar Portal do Azure para executar a ação de script a fim de instalar o Zeppelin, confira [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Você deve fazer algumas alterações nas instruções deste artigo.

* Você deve usar o script para instalar o Zeppelin. O script personalizado para instalar o Zeppelin em um cluster do Spark HDInsight está disponível nos seguintes links:

  * Para clusters do Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Para clusters do Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Você deve executar a ação de script somente no nó principal.
* O script não precisa de parâmetros.

### <a name="using-hdinsight-net-sdk"></a>Usando o SDK do .NET do HDInsight
Para obter instruções sobre como usar o SDK do .NET do HDInsight para executar a ação de script a fim de instalar o Zeppelin, confira [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Você deve fazer algumas alterações nas instruções deste artigo.

* Você deve usar o script para instalar o Zeppelin. O script personalizado para instalar o Zeppelin em um cluster do Spark HDInsight está disponível nos seguintes links:

  * Para clusters do Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Para clusters do Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* O script não precisa de parâmetros.
* Defina o tipo de cluster que você está criando como Spark.

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Use o trecho do PowerShell a seguir para criar um cluster Spark no HDInsight Linux com o Zeppelin instalado. Dependendo da sua versão do cluster do Spark, você deve atualizar o trecho de código do PowerShell abaixo para incluir o link para o script personalizado correspondente.

* Para clusters do Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Para clusters do Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Acessar o bloco de anotações do Zeppelin

Depois de instalar com êxito o Zeppelin usando ação de script, use as etapas a seguir para acessar o notebook Zeppelin no cluster Spark. Nesta seção, você verá como executar instruções %sql e %hive.

1. No navegador da Web, abra o seguinte ponto de extremidade:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Notebook** e em **Criar Nova Anotação**.

    ![Criar um novo notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Criar um novo notebook Zeppelin")

    Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com o nome começando por **Nota XXXXXXXXX**. Clique no novo bloco de anotações.
3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações, se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

    ![Status do notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Status do notebook Zeppelin")

### <a name="run-sql-statements"></a>Executar Instruções SQL
1. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\HdiSamples\SensorSampleData\hvac**.

    No parágrafo vazio criado por padrão no novo bloco de anotações, cole o trecho a seguir.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    Pressione **SHIFT + ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o trecho. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXCECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

    ![Criar uma tabela temporária a partir de dados brutos](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Criar uma tabela temporária a partir de dados brutos")

    Você também pode fornecer um título para cada parágrafo. No canto direito, clique no ícone **Configurações** e em **Mostrar título**.
2. Agora você pode executar instruções do Spark SQL na tabela **hvac** . Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas almejada e real para cada prédio em uma determinada data. Pressione **SHIFT+ENTER**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    A instrução **%sql** no início informa ao bloco de anotações para usar o interpretador Spark SQL. Você pode examinar os interpretadores definidos na guia **Interpretador** no cabeçalho do bloco de anotações.

    A captura de tela a seguir mostra o resultado.

    ![Executar uma instrução SQL do Spark usando o notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Executar uma instrução SQL do Spark usando o notebook")

     Clique nas opções de exibição (realçadas no retângulo) para alternar entre diferentes representações para o mesmo resultado. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave e a média de **temp_diff** como o valor.
3. Você também pode executar instruções Spark SQL usando variáveis na consulta. O seguinte trecho mostra como definir uma variável, **Temp**, na consulta com os possíveis valores com os quais você deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    Cole esse trecho em um novo parágrafo e pressione **SHIFT+ENTER**. A captura de tela a seguir mostra o resultado.

    ![Executar uma instrução SQL do Spark usando o notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Executar uma instrução SQL do Spark usando o notebook")

    Em consultas subsequentes, você pode selecionar um novo valor na lista suspensa e executar a consulta novamente. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa o **buildingID** como chave, a média de **temp_diff** como valor e a **targettemp** como grupo.
4. Reinicie o interpretador do SQL Sparks para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

    ![Reiniciar o intérprete do Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reiniciar o intérprete do Zeppelin")

### <a name="run-hive-statements"></a>Executar Instruções do hive
1. No bloco de anotações Zeppelin, clique no botão **Interpretador** .

    ![Atualizar intérprete do Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Atualizar intérprete do Hive")
2. Para o interpretador de **hive**, clique em **editar**.

    ![Atualizar intérprete do Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Atualizar intérprete do Hive")

    Atualize as seguintes propriedades.

   * Defina **default.password** com a senha especificada para o usuário administrador durante a criação do cluster HDInsight Spark.
   * Defina **default.url** como `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Substitua **\<nome_de_cluster_spark** pelo nome de seu cluster Spark.
   * Defina **default.user** como o nome de usuário administrador especificado durante a criação do cluster. Por exemplo, *administrador*.
3. Clique em **Salvar** e, quando receber a solicitação para reiniciar o interpretador de hive, clique em **OK**.
4. Crie um novo bloco de anotações e execute a instrução a seguir para listar todas as tabelas de hive no cluster.

        %hive
        SHOW TABLES

    Por padrão, um cluster HDInsight tem um exemplo de tabela chamado **hivesampletable** , então você deverá ver a seguinte saída.

    ![Saída do Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Saída do Hive")
5. Execute a instrução a seguir para listar os registros na tabela.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Você deverá ver algo semelhante ao seguinte.

    ![Saída do Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Saída do Hive")

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
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

