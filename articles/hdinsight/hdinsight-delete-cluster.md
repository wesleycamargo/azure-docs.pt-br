<properties
pageTitle="Como excluir um cluster HDInsight | Azure"
description="Informações sobre as várias maneiras pelas quais é possível um excluir um cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/09/2016"
ms.author="larryfr"/>

#Como excluir um cluster HDInsight

A cobrança do cluster do HDInsight começa quando um cluster é criado e é interrompida quando o cluster é excluído e é proporcional por minuto, portanto, você sempre deve excluir o cluster quando ele não estiver mais em uso. Neste documento, você aprenderá a excluir um cluster usando o Portal do Azure, o Azure PowerShell e a CLI do Azure.

> [AZURE.IMPORTANT] A exclusão de um cluster HDInsight não exclui a(s) conta(s) do Armazenamento do Azure associada(s) ao cluster. Isso permite que você preserve e reutilize quaisquer dados armazenados pelo cluster.

##Portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com) e selecione seu cluster HDInsight. Se seu cluster HDInsight não estiver fixado no painel, será possível procurá-lo pelo nome usando o campo de pesquisa (ícone de lupa), no lado direito da barra de navegação.

    ![pesquisa do portal](./media/hdinsight-delete-cluster/navbar.png)

2. Depois que a folha do cluster for aberta, selecione o ícone __Excluir__. Quando solicitado, selecione __Sim__ para excluir o cluster.

    ![excluir ícone](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

Em um prompt do PowerShell, use o seguinte comando para excluir o cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight.

##CLI do Azure

Em um prompt, use o seguinte para excluir o cluster:

    azure hdinsight cluster delete CLUSTERNAME
    
Substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight.

<!---HONumber=AcomDC_0810_2016-->