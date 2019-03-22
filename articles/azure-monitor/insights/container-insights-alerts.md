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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886750"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como configurar alertas para problemas de desempenho no Azure Monitor para contêineres
O Azure Monitor para contêineres monitora o desempenho das cargas de trabalho de contêineres implantadas nas Instâncias de Contêiner do Azure ou nos clusters Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). 

Este artigo descreve como habilitar alertas nas seguintes situações:

* Quando utilização de CPU e memória em nós do cluster ou excede o limite definido.
* Quando a utilização da CPU ou memória em qualquer um dos contêineres dentro de um controlador excede o limite definido em comparação com o limite definido no recurso correspondente.

Para alertar quando a utilização de CPU ou memória é alta para um cluster ou em um controlador, você deve criar uma regra de alerta de métrica de medição que se baseia em consultas de log fornecidas. As consultas comparar uma data e hora atual usando o operador agora e volta de uma hora. Todas as datas armazenadas pelo Azure Monitor para contêineres estão no formato UTC.

Antes de começar, se você não estiver familiarizado com Alertas no Azure Monitor, consulte [Visão geral de alertas no Microsoft Azure](../platform/alerts-overview.md). Para saber mais sobre alertas usando consultas de log, consulte [Alertas do log no Azure Monitor](../platform/alerts-unified-log.md)

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de log de utilização de recursos
As consultas nesta seção são fornecidas para dar suporte a cada cenário de alerta. As consultas são necessárias para a etapa 7 sob o [criar alerta](#create-alert-rule) seção abaixo.  

A consulta a seguir calcula a utilização média da CPU como uma média da utilização da CPU dos nós de membros a cada minuto.  

```kusto
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

```kusto
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
>[!IMPORTANT]
>Consultas abaixo contêm valores de cadeia de caracteres de espaço reservado para os nomes de cluster e do controlador - < your-cluster-name > e < your-controller-name >. Substitua os espaços reservados pelos valores específicos ao seu ambiente antes de configurar alertas. 


A consulta a seguir calcula a utilização média da CPU de todos os contêineres em um controlador como uma média de utilização da CPU de cada instância de contêiner em um controlador a cada minuto como um percentual do limite definido para um contêiner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A consulta a seguir calcula a utilização de memória média de todos os contêineres em um controlador como uma média de utilização de memória de cada instância de contêiner em um controlador como uma porcentagem do limite definido para um contêiner a cada minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="create-alert-rule"></a>Criar regra de alerta
Execute as seguintes etapas para criar um alerta de Log no Azure Monitor usando uma das regras de pesquisa de log fornecidas anteriormente.  

>[!NOTE]
>O procedimento a seguir exige que você alterne para o novo API de alertas de Log, conforme descrito em [preferência de API de alternar para alertas do Log](../platform/alerts-log-api-switch.md) se você estiver criando uma regra de alerta para utilização de recursos do contêiner. 
>

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** do painel esquerdo no portal do Azure. Na seção **Insights**, selecione **Contêineres**.    
3. Na guia **Clusters Monitorados** selecione um cluster na lista, clicando no nome do cluster.
4. No painel esquerdo, na seção **Monitoramento**, selecione **Logs** para abrir a página de logs do Azure Monitor, que era usada para gravar e executar consultas do Azure Log Analytics.
5. Na página **Logs**, clique em **+ Nova regra de alerta**.
6. Na seção **Condição**, clique na condição de log personalizada predefinida **Sempre que a pesquisa de logs personalizada for <logic undefined>**. O tipo de sinal de **pesquisa de logs personalizada** é selecionado automaticamente porque iniciamos a criação de uma regra de alerta diretamente da página de logs do Azure Monitor.  
7. Cole um dos [consultas](#resource-utilization-log-search-queries) fornecidos anteriormente para o **consulta de pesquisa** campo. 

8. Configure o alerta com as informações a seguir:

    a. Na lista suspensa **Com base em**, selecione **Medição métrica**. Uma medição métrica criará um alerta para cada objeto na consulta com um valor que exceda o limite especificado.  
    b. Para a **Condição**, selecione **Maior que** e insira **75** como uma linha de base inicial **Limite** ou insira um valor que atenda aos critérios.  
    c. Na seção **Disparar Alerta com Base Em**, selecione **Violações consecutivas** e na lista suspensa selecione **Maior que** insira um valor de **2**.  
    d. Se configurar um alerta para o contêiner da CPU ou a utilização de memória, sob **agregar** selecionar **ContainerName** na lista suspensa.  
    e. Na seção **Avaliado com base em**, modifique o valor do **Período** para 60 minutos. A regra será executada a cada cinco minutos e retornará registros criados na última hora a partir do horário atual. Definir o período de tempo para uma janela maior considera uma possível latência de dados, garantindo que a consulta retorne dados para evitar um falso negativo em que o alerta nunca é disparado. 

9. Clique em **Concluído** para concluir a regra de alerta.
10. Forneça um nome para o alerta no campo **Nome da regra de alerta**. Especifique uma **Descrição** com detalhes específicos do alerta e selecione uma severidade apropriada das opções fornecidas.
11. Para ativar imediatamente a regra de alerta na criação, aceite o valor padrão para **Habilitar regra após a criação**.
12. Para a etapa final, selecione um **Grupo de Ações** existente ou crie um novo, que garantirá que as mesmas ações serão tomadas sempre que um alerta for disparado e poderá ser usado para cada regra definida. Configure com base em como as operações de DevOps ou TI gerenciam incidentes. 
13. Clique em **Criar regra de alerta** para concluir a regra de alerta. Ela começa a ser executada imediatamente.

## <a name="next-steps"></a>Próximas etapas

* Revise alguns dos [exemplos de consulta de log](container-insights-analyze.md#search-logs-to-analyze-data) para saber mais sobre consultas ou exemplos predefinidos para avaliar e usar ou personalizar para outros cenários de alerta. 
* Para continuar aprendendo como usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, consulte [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md)
