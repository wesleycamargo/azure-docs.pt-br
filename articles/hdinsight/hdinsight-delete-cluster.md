---
title: Como excluir um cluster HDInsight - Azure
description: Informações sobre as várias maneiras pelas quais é possível um excluir um cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097160"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure

A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Neste documento, você aprenderá a excluir um cluster usando o [portal do Azure](https://portal.azure.com), [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)e o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> A exclusão de um cluster HDInsight não exclui as contas do Armazenamento do Azure ou Data Lake Storage associadas ao cluster. Você pode reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, navegue até **todos os serviços** > **Analytics** > **clusters de HDInsight** e selecione seu cluster.

3. A exibição padrão, selecione a **excluir** ícone. Siga os prompts para excluir o cluster.
   
    ![excluir ícone](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Módulo de Az do PowerShell do Azure

Substitua `CLUSTERNAME` com o nome do seu cluster HDInsight no código a seguir. Em um prompt do PowerShell, digite o seguinte comando para excluir o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

Substitua `CLUSTERNAME` com o nome do seu cluster HDInsight, e `RESOURCEGROUP` com o nome do seu grupo de recursos no código a seguir.  Em um prompt de comando, digite o seguinte para excluir o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
