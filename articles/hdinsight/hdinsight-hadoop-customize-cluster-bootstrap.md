---
title: "Personalizar clusters do HDInsight usando a Inicialização | Microsoft Docs"
description: "Saiba como personalizar os clusters HDInsight usando a Inicialização."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a83b4e910dbb8421d80416286c3d1eadcbf7bba7


---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clusters do HDInsight usando a Inicialização
Às vezes, você deseja configurar os arquivos de configuração que incluem:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Os clusters não podem reter as alterações devido à recriação das imagens. Para saber mais sobre a recriação de imagens, consulte [A instância de função é reinicializada devido a atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, você pode usar a personalização de clusters do HDInsight durante o processo de criação. Essa é a maneira recomendável de mudar as configurações de um cluster e persisti-las entre esses eventos de reinicialização do refazimento de imagem do Azure. Essas mudanças de configuração são aplicadas antes do início de um serviço para que os serviços não precisem ser reiniciados. 

Há três métodos para usar a Inicialização:

* Usar PowerShell do Azure
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* Usar o SDK do .NET
* Usar o modelo do Azure Resource Manager

Para obter informações sobre como instalar componentes adicionais no cluster do HDInsight durante o tempo de criação, consulte:

* [Personalizar os clusters HDInsight usando a Ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
* [Personalizar os Clusters HDInsight usando a Ação de Script (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
O seguinte código do PowerShell personaliza uma configuração de Hive:

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

Um script do PowerShell em funcionamento completo pode ser encontrado no [Apêndice A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**Para verificar a alteração:**

1. Entre no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique em **Procurar** e, em seguida, clique em **Clusters do HDInsight**.
3. Clique no cluster que você acabou de criar usando o script do PowerShell.
4. Clique em **Painel** na parte superior da folha para abrir a interface do usuário do Ambari.
5. Clique em **Hive** no menu à esquerda.
6. Clique em **HiveServer2** em **Resumo**.
7. Selecione a guia **Configurações** .
8. Clique em **Hive** no menu à esquerda.
9. Selecione a guia **Avançado** .
10. Role para baixo e expanda **Site Hive Avançado**.
11. Procure **hive.metastore.client.socket.timeout** na seção.

Alguns outros exemplos de personalização de outros arquivos de configuração:

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para obter mais informações, confira o blog de Azim Uddin chamado [Personalizando a criação de Clusters HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Usar o SDK do .NET
Veja [Criar clusters baseados em Linux no HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
Você pode usar o bootstrap no modelo do Resource Manager:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight, hadoop, personalizar cluster, inicialização, modelo do azure resource manager](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Consulte também
* [Criar clusters Hadoop no HDInsight][hdinsight-provision-cluster] fornece instruções sobre como criar um cluster HDInsight usando outras opções personalizadas.
* [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script]
* [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]
* [Instalar e usar o R nos clusters HDInsight][hdinsight-install-r]
* [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Estágios durante a criação do cluster"

## <a name="appx-a-powershell-sample"></a>Apêndice A: exemplo do PowerShell
Esse script do PowerShell cria um cluster do HDInsight e personaliza uma configuração de Hive:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion



<!--HONumber=Nov16_HO3-->


