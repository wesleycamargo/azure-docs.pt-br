---
title: Criar Azure HDInsight (Hadoop) baseados em Windows usando PowerShell | Microsoft Docs
description: Saiba como criar clusters Hadoop baseados no Windows no HDInsight usando o Azure PowerShell.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a365e67d-55bc-476e-a126-68f0962ec5aa
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 9870b61eb00cee169973ae7c09b0c4886cd2db53


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Crie clusters Hadoop baseados no Windows no HDInsight usando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar um cluster Hadoop baseados no Windows no HDInsight usando o Azure PowerShell. 

As informações neste artigo aplicam-se apenas aos clusters HDInsight baseados no Windows. Para obter informações sobre a criação de clusters baseados em Linux, veja [Criar clusters Hadoop no HDInsight usando o Azure Powershell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* Assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* PowerShell do Azure.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Essa seção fornece instruções sobre como criar um cluster HDInsight usando o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

Os procedimentos a seguir são necessários para criar um cluster HDInsight usando o Azure PowerShell:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

## <a name="create-clusters-using-resource-manager-template"></a>Criar clusters usando o modelo do Resource Manager
Você pode usar o Azure PowerShell para implantar um modelo do Azure Resource Manager que cria um cluster do HDInsight.  Consulte [Chamar modelos usando o Azure PowerShell](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-powershell).

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalizar clusters do HDInsight baseados em Windows usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Gerenciar clusters do HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md) - aprenda como trabalhar com o HDInsight usando o Azure PowerShell
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation] ‑ Descubra o SDK do HDInsight

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx



<!--HONumber=Jan17_HO4-->


