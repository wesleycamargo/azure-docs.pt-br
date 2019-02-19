---
title: Criar alertas de desempenho com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como é possível criar alertas personalizados do Azure com base em consultas de log para memória e utilização da CPU do Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238832"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Como configurar alertas no Azure Monitor para problemas de desempenho de contêineres
O Azure Monitor para contêineres monitora o desempenho das cargas de trabalho de contêineres implantadas nas Instâncias de Contêiner do Azure ou nos clusters Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). 

Este artigo descreve como habilitar o alerta quando a utilização da memória e do processador nos nós do cluster exceder o limite definido.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Criar alerta na utilização da memória ou CPU do cluster
Para alertar quando a utilização da memória ou CPU estiver alta para um cluster, crie uma regra de alerta de medida de métrica baseada nas consultas de log fornecidas. As consultas comparam um datetime com o presente usando o operador `now` e recua uma hora. Todas as datas armazenadas pelo Azure Monitor para contêineres estão no formato UTC.  

Antes de começar, se você não estiver familiarizado com Alertas no Azure Monitor, consulte [Visão geral de alertas no Microsoft Azure](../platform/alerts-overview.md). Para saber mais sobre alertas usando consultas de log, consulte [Alertas do log no Azure Monitor](../platform/alerts-unified-log.md)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** do painel esquerdo no portal do Azure. Na seção **Insights**, selecione **Contêineres**.    
3. Na guia **Clusters Monitorados** selecione um cluster na lista, clicando no nome do cluster.
4. No painel esquerdo, na seção **Monitoramento**, selecione **Logs** para abrir a página de logs do Azure Monitor, que era usada para gravar e executar consultas do Azure Log Analytics.
5. Na página **Logs**, clique em **+ Nova regra de alerta**.
6. Na seção **Condição**, clique na condição de log personalizada predefinida **Sempre que a pesquisa de logs personalizada for <logic undefined>**. O tipo de sinal de **pesquisa de logs personalizada** é selecionado automaticamente porque iniciamos a criação de uma regra de alerta diretamente da página de logs do Azure Monitor.  
7. Cole uma das consultas abaixo no campo **Consulta de pesquisa**. A consulta a seguir calcula a utilização média da CPU como uma média da utilização da CPU dos nós de membros a cada minuto.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    A consulta a seguir calcula a utilização média da memória como uma média da utilização da memória dos nós de membros a cada minuto.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Configure o alerta com as informações a seguir:

     a. Na lista suspensa **Com base em**, selecione **Medição métrica**. Uma medição métrica criará um alerta para cada objeto na consulta com um valor que exceda o limite especificado.  
    b. Para a **Condição**, selecione **Maior que** e insira **75** como uma linha de base inicial **Limite** ou insira um valor que atenda aos critérios.  
    c. Na seção **Disparar Alerta com Base Em**, selecione **Violações consecutivas** e na lista suspensa selecione **Maior que** insira um valor de **2**.  
    d. Na seção **Avaliado com base em**, modifique o valor do **Período** para 60 minutos. A regra será executada a cada cinco minutos e retornará registros criados na última hora a partir do horário atual. Definir o período de tempo para uma janela maior considera uma possível latência de dados, garantindo que a consulta retorne dados para evitar um falso negativo em que o alerta nunca é disparado. 

9. Clique em **Concluído** para concluir a regra de alerta.
10. Forneça um nome para o alerta no campo **Nome da regra de alerta**. Especifique uma **Descrição** com detalhes específicos do alerta e selecione uma severidade apropriada das opções fornecidas.
11. Para ativar imediatamente a regra de alerta na criação, aceite o valor padrão para **Habilitar regra após a criação**.
12. Para a etapa final, selecione um **Grupo de Ações** existente ou crie um novo, que garantirá que as mesmas ações serão tomadas sempre que um alerta for disparado e poderá ser usado para cada regra definida. Configure com base em como as operações de DevOps ou TI gerenciam incidentes. 
13. Clique em **Criar regra de alerta** para concluir a regra de alerta. Ela começa a ser executada imediatamente.

## <a name="next-steps"></a>Próximas etapas

* Revise alguns dos [exemplos de consulta de log](container-insights-analyze.md#search-logs-to-analyze-data) para saber mais sobre consultas ou exemplos predefinidos para avaliar e usar ou personalizar para outros cenários de alerta. 
* Para continuar aprendendo como usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, consulte [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md)
