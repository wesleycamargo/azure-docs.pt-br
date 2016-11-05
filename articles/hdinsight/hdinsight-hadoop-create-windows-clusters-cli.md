---
title: Crie clusters Hadoop baseados no Windows no HDInsight usando a CLI do Azure
description: Aprenda a criar clusters do Azure HDInsight usando a CLI do Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Crie clusters Hadoop baseados no Windows no HDInsight usando a CLI do Azure
[!INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar clusters do HDInsight usando a CLI do Azure. Para outros recursos e ferramentas de criação de cluster, clique na guia Selecionar na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

## Pré-requisitos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* **Assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI do Azure**
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## Conecte-se ao Azure
Conecte-se ao Azure usando o seguinte comando:

    azure login

Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).

Execute o seguinte comando para alternar para o modo de ARM:

    azure config mode arm

Para obter ajuda, use a opção **-h**. Por exemplo:

    azure hdinsight cluster create -h

## Criar clusters
Você deve ter um ARM (Gerenciamento de Recursos do Azure) e uma conta de armazenamento de Blob do Azure antes de poder criar um cluster HDInsight. Para criar um cluster HDInsight, você deve especificar o seguinte:

* **Grupo de Recursos do Azure**: é necessário criar uma conta da Análise Data Lake em um grupo de Recursos do Azure. O Gerenciador de Recursos do Azure permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.
  
    Para listar os grupos de recursos em sua assinatura:
  
        azure group list
  
    Para criar um novo grupo de recursos:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nome do cluster HDInsight**
* **Local**: um dos datacenters do Azure que dá suporte aos clusters HDInsight. Para obter uma lista dos locais com suporte, confira [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Conta de armazenamento padrão** - o HDInsight usa um contêiner de armazenamento do Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight.
  
    Para criar uma nova conta de armazenamento do Azure:
  
        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS
  
  > [!NOTE]
  > A conta de armazenamento deve ser colocada com o HDInsight no data center. O tipo de conta de armazenamento não pode ser ZRS, porque o ZRS não dá suporte à tabela.
  > 
  > 
  
    Para obter informações sobre como criar uma conta de Armazenamento do Azure usando o Portal do Azure, consulte [Criar, gerenciar ou excluir uma conta de Armazenamento][azure-create-storageaccount].
  
    Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:
  
        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"
  
    Para obter detalhes sobre como obter as informações usando o Portal do Azure, veja a seção “Gerenciar sua conta de armazenamento” em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md#manage-your-storage-account).
* **Contêiner de Blob Padrão (Opcional)**: o comando **azure hdinsight cluster create** cria o contêiner caso ele não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:
  
    azure storage container create --account-name "<Nome da conta de armazenamento>" --account-key <Chave da conta de armazenamento> [ContainerName]

Depois de preparar a conta de Armazenamento, você estará pronto para criar um cluster:

    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


## Criar clusters usando arquivos de configuração
Normalmente, você cria um cluster HDInsight, executa trabalhos nele e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, para que você possa reutilizá-lo toda vez que criar um cluster.

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemplo: crie um arquivo de configuração que contém uma ação de script para ser executada durante a criação de um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

## Criar clusters com uma ação de script
Crie um arquivo de configuração que contém uma ação de script para ser executada durante a criação de um cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Criar um cluster com uma ação de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Para obter informações gerais sobre ação de script, veja [Personalizar clusters HDInsight usando a Ação de Script (Linux)](hdinsight-hadoop-customize-cluster.md).

## Criar clusters usando modelos de ARM
Você pode usar a CLI para criar clusters chamando modelos de ARM. Consulte [Implantar com a CLI do Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## Confira também
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Gerenciar clusters Hadoop no HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Serviços do Azure](../virtual-machines-command-line-tools.md)

<!---HONumber=AcomDC_0914_2016-->