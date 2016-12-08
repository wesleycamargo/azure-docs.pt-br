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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Como excluir um cluster HDInsight
A cobrança do cluster do HDInsight começa quando um cluster é criado e é interrompida quando o cluster é excluído e é proporcional por minuto, portanto, você sempre deve excluir o cluster quando ele não estiver mais em uso. Neste documento, você aprenderá a excluir um cluster usando o Portal do Azure, o Azure PowerShell e a CLI do Azure.

> [!IMPORTANT]
> A exclusão de um cluster HDInsight não exclui a(s) conta(s) do Armazenamento do Azure associada(s) ao cluster. Isso permite que você preserve e reutilize quaisquer dados armazenados pelo cluster.
> 
> 

## <a name="azure-portal"></a>Portal do Azure
1. Faça logon no [portal do Azure](https://portal.azure.com) e selecione seu cluster HDInsight. Se seu cluster HDInsight não estiver fixado no painel, será possível procurá-lo pelo nome usando o campo de pesquisa (ícone de lupa), no lado direito da barra de navegação.
   
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




<!--HONumber=Nov16_HO3-->


