---
title: Criar alertas de desempenho usando o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como usar o Azure Monitor para contêineres para criar alertas personalizados com base em consultas de log para a utilização de memória e CPU.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: bbd7c733c7c089328d2fbe016426fe9de3a6b5ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494619"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como configurar alertas para problemas de desempenho no Azure Monitor para contêineres
O Azure Monitor para contêineres monitora o desempenho de cargas de trabalho de contêiner que são implantados em instâncias de contêiner do Azure ou para clusters de Kubernetes hospedados no serviço de Kubernetes do Azure (AKS) gerenciados.

Este artigo descreve como habilitar alertas nas seguintes situações:

* Quando a utilização da CPU ou memória em nós de cluster excede um limite definido
* Quando a utilização da CPU ou memória em qualquer contêiner dentro de um controlador excede um limite definido em comparação com um limite que é definido no recurso correspondente
* *NotReady* contagens de nó de status
*  *Falha na*, *pendente*, *desconhecido*, *executando*, ou *bem-sucedido* contagens de pod fase

Para alertar para alta utilização da CPU ou a utilização de memória em nós de cluster, use as consultas que são fornecidas para criar um alerta de métrica ou um alerta de métrica de medição. Alertas de métrica têm a menor latência que os alertas do log. Mas, alertas de log fornecem consultas avançadas e a sofisticação maior. Consultas comparam uma data e hora atual por meio de alertas de log a *agora* operador e vai fazer uma hora. (O azure Monitor para contêineres armazena todas as datas no formato Tempo Universal Coordenado (UTC).)

Se você não estiver familiarizado com os alertas do Azure Monitor, consulte [visão geral dos alertas no Microsoft Azure](../platform/alerts-overview.md) antes de começar. Para saber mais sobre alertas que usam consultas de log, consulte [alertas de Log no Azure Monitor](../platform/alerts-unified-log.md). Para obter mais informações sobre alertas de métrica, consulte [alertas de métrica no Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de log de utilização de recursos
As consultas desta seção dão suporte a cada cenário de alerta. Eles são usados na etapa 7 da [criar alerta](#create-an-alert-rule) seção deste artigo.

A consulta a seguir calcula a utilização média da CPU como uma média de utilização da CPU de nós de membro a cada minuto.  

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

A consulta a seguir calcula a utilização média da memória como uma média de utilização de memória de nós de membro a cada minuto.

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
>As seguintes consultas usam os valores de espaço reservado \<your-cluster-name > e \<seu nome de controlador > para representar seu cluster e o controlador. Substitua-os com valores específicos ao seu ambiente quando você configura alertas.

A consulta a seguir calcula a utilização média da CPU de todos os contêineres em um controlador como uma média de utilização da CPU de cada instância de contêiner em um controlador a cada minuto. A medida é uma porcentagem do limite definido para um contêiner.

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

A consulta a seguir calcula a utilização de memória média de todos os contêineres em um controlador como uma média de utilização de memória de cada instância de contêiner em um controlador a cada minuto. A medida é uma porcentagem do limite definido para um contêiner.

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

A consulta a seguir retorna todos os nós e que têm um status de contagens *pronto* e *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A consulta a seguir retorna contagens de fase de pod com base em todas as fases: *Falha na*, *pendente*, *desconhecido*, *executando*, ou *bem-sucedida*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Para alertar sobre determinadas fases de pod, tais como *pendente*, *Failed*, ou *desconhecido*, modifique a última linha da consulta. Por exemplo, para alertar sobre *FailedCount* usar: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Siga estas etapas para criar um alerta do log no Azure Monitor, usando uma das regras de pesquisa de log fornecidos anteriormente.  

>[!NOTE]
>O procedimento a seguir para criar uma regra de alerta para utilização de recursos do contêiner requer que você alterne para um novo log de alertas API conforme descrito em [preferência de API de alternar para alertas de log](../platform/alerts-log-api-switch.md).
>

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** no painel à esquerda. Sob **Insights**, selecione **contêineres**.
3. Sobre o **monitorados Clusters** guia, selecione um cluster da lista.
4. No painel à esquerda sob **Monitoring**, selecione **Logs** para abrir a página de logs do Azure Monitor. Use esta página para criar e executar consultas do Log Analytics do Azure.
5. Sobre o **Logs** página, selecione **+ nova regra de alerta**.
6. No **condição** seção, selecione a **sempre que o log pesquisa personalizada é \<lógica não definida >** predefinida pela condição de log personalizado. O **pesquisa de logs personalizada** tipo de sinal é selecionado automaticamente, pois estamos criando uma regra de alerta diretamente da página de logs do Azure Monitor.  
7. Cole um dos [consultas](#resource-utilization-log-search-queries) fornecidos anteriormente para o **consulta de pesquisa** campo.
8. Configure o alerta da seguinte maneira:

    1. Na lista suspensa **Com base em**, selecione **Medição métrica**. Uma métrica medida cria um alerta para cada objeto na consulta que tem um valor acima do nosso limite especificado.
    1. Para **condição**, selecione **maior**e insira **75** como uma linha de base inicial **limite**. Ou insira um valor diferente que atenda aos seus critérios.
    1. No **gatilho de alerta com base na** seção, selecione **violações consecutivas**. Na lista suspensa, selecione **maior que**e insira **2**.
    1. Para configurar um alerta para o contêiner da CPU ou a utilização de memória, sob **agregar**, selecione **ContainerName**. 
    1. No **avaliadas com base no** seção, defina as **período** valor para **60 minutos**. A regra será executada a cada 5 minutos e retornar os registros que foram criados na última hora da hora atual. Definir o período de tempo às contas janela ampla para latência de dados potencial. Ela também garante que a consulta retorna dados para evitar um falso negativo em que o alerta nunca é acionado.

9. Selecione **feito** para concluir a regra de alerta.
10. Insira um nome na **nome da regra de alerta** campo. Especifique um **descrição** que fornece detalhes sobre o alerta. E selecione um nível de gravidade apropriadas entre as opções fornecidas.
11. Para ativar a regra de alerta imediatamente, aceite o valor padrão para **Habilitar regra após a criação**.
12. Selecione uma existente **grupo de ação** ou criar um novo grupo. Essa etapa garante que as mesmas ações são executadas sempre que um alerta é disparado. Configurar com base em como sua equipe de TI ou DevOps equipe de operações gerencia os incidentes.
13. Selecione **criar regra de alerta** para concluir a regra de alerta. Ela começa a ser executada imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Modo de exibição [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertas, visualizar ou analisar seus clusters.
- Para saber mais sobre como monitorar outros aspectos do seu cluster do AKS e o Azure Monitor, consulte [integridade do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
