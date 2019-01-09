---
title: Como excluir um cluster HDInsight - Azure
description: Informações sobre as várias maneiras pelas quais é possível um excluir um cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f5c2f6104a765c0e598e41234891c492686019dd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718972"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure Clássico

A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Neste documento, você aprende como excluir um cluster usando o [Portal do Microsoft Azure](https://portal.azure.com), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) e a CLI Clássica do Azure.

> [!IMPORTANT]  
> A exclusão de um cluster HDInsight não exclui as contas do Armazenamento do Azure ou Data Lake Storage associadas ao cluster. Você pode reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com) e selecione seu cluster HDInsight. Se seu cluster HDInsight não estiver fixado no painel, será possível procurá-lo pelo nome usando o campo de pesquisa.
   
    ![pesquisa do portal](./media/hdinsight-delete-cluster/navbar.png)

2. Nas configurações de cluster, selecione o ícone **Excluir**. Quando solicitado, selecione **Sim** para excluir o cluster.
   
    ![excluir ícone](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Em um prompt do PowerShell, use o seguinte comando para excluir o cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

## <a name="azure-classic-cli"></a>CLI clássica do Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Em um prompt, use o seguinte para excluir o cluster:

    azure hdinsight cluster delete CLUSTERNAME

Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.
