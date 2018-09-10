---
title: Use o Azure Log Analytics para monitorar clusters Azure HDInsight
description: Saiba como usar o Azure Log Analytics para monitorar trabalhos sendo executados em um cluster HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 87d1593327ab45f064c6f6b19dfbe3882baaa446
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109509"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Use Azure Log Analytics para monitorar clusters HDInsight

Saiba como habilitar o Azure Log Analytics para monitorar as operações de cluster de Hadoop no HDInsight e como adicionar uma solução de monitoramento do Azure HDInsight.

O [Log Analytics](../log-analytics/log-analytics-overview.md) é um serviço que monitora os ambientes locais e de nuvem para manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Um espaço de trabalho do Log Analytics**. É possível pensar neste espaço de trabalho como um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

* **Um cluster Azure HDInsight**. Atualmente, é possível usar o Log Analytics com os tipos de Cluster HDInsight a seguir:

  * O Hadoop
  * HBase
  * Consulta Interativa
  * Kafka
  * Spark
  * Storm

  Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]
> É recomendável colocar o cluster HDInsight e o espaço de trabalho do Log Analytics na mesma região para melhorar o desempenho. Observe o que Azure Log Analytics não está disponível em todas as regiões do Azure.

## <a name="enable-log-analytics-by-using-the-portal"></a>Habilitar o Log Analytics no portal

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. Abra o cluster HDInsight no portal do Azure.
2. No painel esquerdo, selecione **Monitoramento**.
3. No painel direito, selecione **Habilitar** e, selecione um espaço de trabalho do Log Analytics existente e selecione **Salvar**.

    ![Habilitar o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Habilitar o monitoramento para clusters HDInsight")

    Levará alguns minutos para salvar a configuração.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Habilitar o Log Analytics no Azure PowerShell

Você pode habilitar o Log Analytics usando o Azure PowerShell. O cmdlet é:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

Consulte [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Para desabilitar, o cmdlet é:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

Consulte [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar solução de gerenciamento do cluster do Microsoft Azure HDInsight

O HDInsight fornece soluções de gerenciamento específicas para cluster que podem ser adicionadas para o Azure Log Analytics. As [soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) adicionam funcionalidade ao Log Analytics, fornecendo dados adicionais e ferramentas de análise. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight e fornecem as ferramentas para pesquisá-las. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas.

Estas são as soluções de Microsoft Azure HDInsight disponíveis:

* Monitoramento do Hadoop no HDInsight
* Monitoramento do HDInsight HBase
* Monitoramento de Consulta Interativo do Microsoft Azure HDInsight
* Monitoramento do Kafka no HDInsight
* Monitoramento do Spark no HDInsight
* Monitoramento do Storm no HDInsight

Para obter instruções para instalar uma solução de gerenciamento, consulte [Soluções de Gerenciamento do Azure](../monitoring/monitoring-solutions.md#install-a-management-solution). Para fazer experimento, instale uma solução de Monotiring de Hadoop do Microsoft Azure HDInsight. Quando estiver pronto, você verá uma peça **HDInsightHadoop** listada em **Resumo**. Selecione a peça **HDInsightHadoop**. A solução HDInsightHadoop é semelhante a:

![Exibição da solução de monitoramento Hadoop OMS do Microsoft Azure HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é uma marca nova, o relatório não mostra todas as atividades.

## <a name="next-steps"></a>Próximas etapas

* [Consultar o Azure Log Analytics ara monitorar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
