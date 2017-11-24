---
title: Consultar o Azure Log Analytics para monitorar clusters Azure HDInsight | Microsoft Docs
description: "Saiba como executar consultas no Azure Log Analytics para monitorar trabalhos que estão sendo executados em um cluster HDInsight."
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
ms.openlocfilehash: e4ee80826a710bde9483d130a4d1c986a72645ca
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Consultar o Azure Log Analytics para monitorar clusters HDInsight

Saiba mais sobre alguns cenários básicos de como usar o Azure Log Analytics para monitorar os clusters Azure HDInsight:

* [Analisar métricas de cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Procurar as mensagens de log específicas](#search-for-specific-log-messages)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter configurado um cluster HDInsight para usar o Azure Log Analytics. Para obter instruções, consulte [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Usar o Azure Log Analytics com clusters HDInsight).

* Você deverá ter adicionado as soluções de gerenciamento específicas para o cluster HDInsight ao espaço de trabalho do OMS conforme descrito em [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Adicionar soluções de gerenciamento do cluster HDInsight ao Log Analytics).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas de cluster HDInsight

Saiba como procurar métricas específicas para seu cluster HDInsight.

1. Abra um cluster HDInsight que você associou ao Azure Log Analytics no portal do Azure.
2. Clique em **Monitoramento** e então clique em **Abrir Painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. Clique em **Pesquisa de Log** no menu à esquerda.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Digite a consulta a seguir na caixa de pesquisa para procurar todas as métricas disponíveis para todos os clusters HDInsight configurados para usar o Azure Log Analytics e então pressione **ENTER**.

        `search *` 

    ![Pesquisar todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

    A saída deve se parecer com:

    ![Pesquisar saída de todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Pesquisar saída de todas as métricas")

5. No painel esquerdo, em **Tipo**, selecione uma métrica que você deseja examinar em profundidade e então clique em **Aplicar**. A captura de tela mostra que o tipo `metrics_resourcemanager_queue_root_default_CL` é selecionado. 

    > [!NOTE]
    > Talvez você precise clicar no botão **[+]Mais** para localizar a métrica que você está procurando. Além disso, o botão **Aplicar** está na parte inferior da lista. Portanto, você deve rolar para baixo para vê-lo.
    > 
    >    

    Observe que a consulta na caixa de texto muda para a mostrada na caixa realçada na captura de tela a seguir:

    ![Pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisar métricas específicas")

6. Para obter detalhes sobre esta métrica específica. Por exemplo, é possível refinar a saída existente com base na média de recursos usados em um intervalo de 10 minutos, categorizada por nome do cluster usando esta consulta:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Em vez de refinar com base na média de recursos usados, é possível usar a seguinte consulta para refinar os resultados com base em quando os recursos máximos foram usados (assim como o 90º e o 95º percentil) em uma janela de 10 minutos:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Procure as mensagens de log específicas

Saiba como procurar mensagens de erro durante uma janela de tempo específica. Estas etapas são apenas um exemplo de como você pode chegar à mensagem de erro na qual você está interessado. É possível usar qualquer propriedade disponível para procurar os erros que você está tentando localizar.

1. Abra um cluster HDInsight que você associou ao Azure Log Analytics no portal do Azure.
2. Clique em **Monitoramento**e clique em **Abrir Painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. No painel do OMS, na tela inicial, clique em **Pesquisa de logs**.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Digite a consulta a seguir para procurar todas as mensagens de erro para todos os clusters HDInsight configurados para usar o Azure Log Analytics e então pressione **ENTER**. 

         search "Error"

    Você verá uma saída semelhante à esta saída:

    ![Pesquisar a saída de todos os erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Pesquisar a saída de todos os erros")

5. No painel esquerdo, na categoria **Tipo**, selecione um tipo de erro que você deseja ver de maneira aprofundada e então clique em **Aplicar**.  Observe que os resultados são refinados para mostrar apenas o erro do tipo selecionado.
7. É possível obter mais detalhes sobre essa lista de erros específica usando as opções disponíveis no painel esquerdo. Por exemplo, 

    - Para ver mensagens de erro de um nó de trabalho específico:

        ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Pesquisar saída de erros específicos")

    - Para ver se ocorreu um erro em uma determinada hora:

        ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Pesquisar saída de erros específicos")

9. Para ver o erro específico. É possível clicar em **[+] mostrar mais** para examinar a mensagem de erro real.

    ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Pesquisar saída de erros específicos")

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreamento

A primeira etapa para criar um alerta é chegar a uma consulta com base na qual o alerta é disparado. Para simplificar, vamos usar a consulta a seguir que fornece a lista de aplicativos com falha em execução nos clusters HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

É possível usar qualquer consulta que você desejar para criar um alerta.

1. Abra um cluster HDInsight que você associou ao Azure Log Analytics no portal do Azure.
2. Clique em **Monitoramento** e então clique em **Abrir Painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. No painel do OMS, na tela inicial, clique em **Pesquisa de logs**.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Execute a consulta a seguir na qual você deseja criar um alerta e, em seguida, pressione **ENTER**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Clique em **Alerta** na parte superior da página.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Inserir consulta para criar um alerta")

4. Na janela **Adicionar regra de alerta**, insira a consulta e outros detalhes para criar um alerta e, em seguida, clique em **Salvar**.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Inserir consulta para criar um alerta")

    A captura de tela mostra a configuração para o envio de uma notificação por email quando a consulta alerta retornar uma saída.

5. Também é possível editar ou excluir um alerta existente. Para fazer isso, em qualquer página no portal do OMS, clique no ícone **Configurações**.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Inserir consulta para criar um alerta")

6. Na página **Configurações**, clique em **Alertas** para ver os alertas que você criou. Também é possível habilitar ou desabilitar um alerta, editá-lo ou excluí-lo. Para obter mais informações, consulte [Trabalhando com regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Consulte também

* [Trabalhando com o Log Analytics do OMS](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
