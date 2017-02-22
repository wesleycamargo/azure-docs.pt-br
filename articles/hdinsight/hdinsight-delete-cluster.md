---
title: Como excluir um cluster HDInsight | Microsoft Docs
description: "Informações sobre as várias maneiras pelas quais é possível um excluir um cluster HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 8b1fbc541b3123961d092c63441b804109865f60


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Como excluir um cluster HDInsight

A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Neste documento, você aprenderá a excluir um cluster usando o Portal do Azure, o Azure PowerShell e a CLI do Azure.

> [!IMPORTANT]
> A exclusão de um cluster HDInsight não exclui as contas do Armazenamento do Azure associadas ao cluster. Como a conta de armazenamento não é excluída, os dados são mantidos e podem ser reutilizados posteriormente.

## <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com) e selecione seu cluster HDInsight. Se seu cluster HDInsight não estiver fixado no painel, será possível procurá-lo pelo nome usando o campo de pesquisa.
   
    ![pesquisa do portal](./media/hdinsight-delete-cluster/navbar.png)

2. Depois que a folha do cluster for aberta, selecione o ícone **Excluir**. Quando solicitado, selecione **Sim** para excluir o cluster.
   
    ![excluir ícone](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Em um prompt do PowerShell, use o seguinte comando para excluir o cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

## <a name="azure-cli"></a>CLI do Azure

Em um prompt, use o seguinte para excluir o cluster:

    azure hdinsight cluster delete CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.




<!--HONumber=Feb17_HO2-->


