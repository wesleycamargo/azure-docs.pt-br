---
title: Usar o Log Analytics para monitorar clusters HDInsight do Azure | Microsoft Docs
description: Saiba como usar o Azure Log Analytics para monitorar trabalhos sendo executados em um cluster HDInsight.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: 73c472140861a0d0d270021ab268e8c1113c23b5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Use Azure Log Analytics para monitorar clusters HDInsight

Aprenda como usar Azure Log Analytics para monitorar operações de cluster Hadoop em HDInsight.

O [Log Analytics](../log-analytics/log-analytics-overview.md) é um serviço no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que monitora seus ambientes na nuvem e locais a fim de manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes. 

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

* **Um cluster Azure HDInsight**. No momento, é possível usar o Azure OMS com os seguintes tipos de cluster HDInsight:

    * O Hadoop
    * HBase
    * Consulta Interativa
    * Kafka
    * Spark
    * Storm

    Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Um espaço de trabalho do Log Analytics**. É possível pensar neste espaço de trabalho como um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções. É necessário ter um desse espaço de trabalho já criado que você pode associar aos clusters do Azure HDInsight. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Configurar o cluster HDInsight para usar o Log Analytics

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. Abra o cluster HDInsight no portal do Azure.
2. No painel esquerdo, clique em **Monitorando**.
3. No painel direito, clique em **Habilitar** e, em seguida, selecione um espaço de trabalho do Log Analytics existente e clique em **Salvar**.

    ![Habilitar o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Habilitar o monitoramento para clusters HDInsight")

    Levará alguns minutos para salvar a configuração.  Depois que for feito, você pode ver um botão **Abrir o Painel do OMS** na parte superior. 

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Abrir painel do OMS")

5. Clique em **Abrir painel do OMS**.
6. Insira suas credenciais do Azure, se solicitado.

    ![Portal do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portal do Operations Management Suite")

## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções de gerenciamento de cluster HDInsight para o Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
