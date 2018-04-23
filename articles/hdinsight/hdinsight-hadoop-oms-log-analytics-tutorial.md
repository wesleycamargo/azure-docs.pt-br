---
title: Usar o Log Analytics para monitorar clusters HDInsight do Azure | Microsoft Docs
description: Saiba como usar o Azure Log Analytics para monitorar trabalhos sendo executados em um cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Use Azure Log Analytics para monitorar clusters HDInsight

Aprenda como usar Azure Log Analytics para monitorar operações de cluster Hadoop em HDInsight.

O [Log Analytics](../log-analytics/log-analytics-overview.md) é um serviço que monitora os ambientes locais e de nuvem para manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes. 

## <a name="prerequisites"></a>pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

* **Um cluster Azure HDInsight**. Atualmente, é possível usar o Log Analytics com os tipos de Cluster HDInsight a seguir:

    * O Hadoop
    * HBase
    * Consulta Interativa
    * Kafka
    * Spark
    * Storm

    Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um espaço de trabalho do Log Analytics**. É possível pensar neste espaço de trabalho como um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções. É necessário ter um desse espaço de trabalho já criado que você pode associar aos clusters do Azure HDInsight. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Habilitar o Log Analytics no portal

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. Abra o cluster HDInsight no portal do Azure.
2. No painel esquerdo, clique em **Monitorando**.
3. No painel direito, clique em **Habilitar** e, em seguida, selecione um espaço de trabalho do Log Analytics existente e clique em **Salvar**.

    ![Habilitar o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Habilitar o monitoramento para clusters HDInsight")

    Levará alguns minutos para salvar a configuração.  Depois que for feito, você pode ver um botão **Abrir o Painel do OMS** na parte superior. 

    ![Abrir o painel do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Abrir painel do OMS")

5. Clique em **Abrir painel do OMS**.
6. Insira suas credenciais do Azure, se solicitado.

    ![Portal do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portal do Operations Management Suite")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Habilitar o Log Analytics no Azure PowerShell

Você pode habilitar o Log Analytics usando o Azure PowerShell. O cmdlet é:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Consulte [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Para desabilitar, o cmdlet é 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Consulte [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções de gerenciamento de cluster HDInsight para o Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
