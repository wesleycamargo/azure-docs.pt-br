---
title: Gerenciar clusters do Hadoop usando a CLI do Azure | Microsoft Docs
description: Como usar a CLI do Azure para gerenciar clusters do Hadoop no HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e70e24951481c33525a852479c9c9647a45e71ac
ms.lasthandoff: 03/21/2017


---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gerenciar clusters Hadoop no HDInsight usando a CLI do Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar a [Interface de Linha de Comando do Azure](../cli-install-nodejs.md) para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

Este artigo aborda apenas o uso da CLI do Azure com o HDInsight. Para obter um guia geral sobre como usar a CLI do Azure, confira [Instalar e configurar a CLI do Azure][azure-command-line-tools].

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) para obter informações de instalação e configuração.
* **Conecte-se ao Azure**usando o seguinte comando:
  
        azure login
  
    Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).
* **Alterne para o modo Gerenciador de Recursos do Azure**usando o seguinte comando:
  
        azure config mode arm

Para obter ajuda, use a opção **-h** .  Por exemplo:

    azure hdinsight cluster create -h

## <a name="create-clusters"></a>Criar clusters
Consulte [Criar clusters baseados em Linux no HDInsight usando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Excluir clusters
Use o seguinte comando para excluir um cluster:

    azure hdinsight cluster delete <Cluster Name>

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

    azure group delete <Resource Group Name>

## <a name="scale-clusters"></a>Dimensionar clusters
Para alterar o tamanho do cluster Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Habilitar/desabilitar o acesso HTTP para um cluster
    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Habilitar/desabilitar o acesso RDP para um cluster
      azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
      azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar clusters HDInsight usando o Azure PowerShell][hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Como usar a CLI do Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"

