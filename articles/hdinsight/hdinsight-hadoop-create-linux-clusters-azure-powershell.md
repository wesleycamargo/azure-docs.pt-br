---
title: Criar Azure HDInsight (Hadoop) usando PowerShell | Microsoft Docs
description: Saiba como criar clusters Hadoop, HBase, Storm ou Spark no Linux para o HDInsight usando o Azure PowerShell.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: f22fc9e9306d34b66e89966a0f4485461539899d
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um ambiente de script poderoso que você pode usar para controlar e para automatizar a implantação e o gerenciamento de suas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster HDInsight baseadas em Linux usando o Azure PowerShell. Ele também inclui um script de exemplo.

> [!NOTE]
> O Azure PowerShell só está disponível em clientes do Windows. Se você estiver usando um cliente Linux, Unix ou Mac OS X, veja [Criar um cluster HDInsight baseado em Linux usando a CLI Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obter informações sobre como usar a CLI do Azure para criar um cluster.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte antes de iniciar este procedimento:

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [PowerShell do Azure](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e foi removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster HDInsight usando o Azure PowerShell, siga estes procedimentos:

* Criar um grupo de recursos do Azure
* Criar uma conta de Armazenamento do Azure
* Criar um contêiner de Blob do Azure
* Crie um cluster HDInsight

Os dois parâmetros mais importantes que devem ser definidos para criar clusters Linux são aqueles que especificam o tipo de sistema operacional e os detalhes do usuário SSH:

* Garanta que você especificou o parâmetro **-OSType** como **Linux**.
* Para usar SSH para sessões remotas nos clusters, você pode especificar a senha do usuário SSH ou a chave pública SSH. Se você especificar tanto a senha do usuário SSH, quanto a chave pública SSH, a chave será ignorada. Se quiser usar a chave SSH para sessões remotas, você deverá especificar uma senha SSH em branco quando for solicitada. Para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O script a seguir demonstra como criar um novo cluster:

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Os valores especificados para **$clusterCredentials** são usados para criar a conta de usuário do Hadoop para o cluster. Use essa conta para se conectar ao cluster.

Os valores especificados para **$sshCredentials** são usados para criar o usuário SSH para o cluster. Use essa conta para iniciar uma sessão SSH remota no cluster e executar trabalhos.

> [!IMPORTANT]
> Nesse script, você deve especificar o número de nós de trabalho que estarão no cluster. Se você planeja ter mais de 32 nós de trabalho (seja na criação do cluster ou em seu dimensionamento após a criação), deverá também especificar um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Criar cluster: objeto de configuração

Você também pode criar um objeto de configuração de HDInsight usando o cmdlet `New-AzureRmHDInsightClusterConfig`. Será possível, então, modificar esse objeto de configuração para habilitar as opções de configuração adicionais para o cluster. Por fim, use o parâmetro `-Config` do cmdlet `New-AzureRmHDInsightCluster` para usar a configuração.

O script a seguir cria um objeto de configuração para configurar um R Server no tipo de cluster HDInsight. A configuração habilita um nó de borda, RStudio e uma conta de armazenamento adicionais.

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName `
        -StorageAccountName $additionalStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    # Create a new configuration for RServer cluster type
    # Use -EdgeNodeSize to set the size of the edge node for RServer clusters
    # if you want a specific size. Otherwise, the default size is used.
    $config = New-AzureRmHDInsightClusterConfig `
        -ClusterType "RServer" `
        -EdgeNodeSize "Standard_D12_v2"

    # Add RStudio to the configuration
    $rserverConfig = @{"RStudio"="true"}
    $config = $config | Add-AzureRmHDInsightConfigValues `
        -RServer $rserverConfig

    # Add an additional storage account
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -OSType Linux `
        -Version "3.5" `
        -SshCredential $sshCredentials `
        -Config $config

> [!WARNING]
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight. Ao usar este exemplo, crie a conta de armazenamento adicional no mesmo local do servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use os seguintes recursos para aprender a trabalhar com o seu cluster.

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters do Spark

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)


