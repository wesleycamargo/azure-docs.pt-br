---
title: Alertas de métrica mais recentes nos recursos com suporte no Azure Monitor | Microsoft Docs
description: Referência sobre métricas e logs de suporte para novos alertas de métrica do Azure quase em tempo real.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 6ccb095f3739a90bdab2408965a742f9cbc19359
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Alertas de métrica mais recentes para serviços do Azure no Portal do Azure
O Azure Monitor agora fornece suporte a um novo tipo de alerta de métrica. Os alertas mais recentes diferem dos [alertas de métrica clássicos](insights-alerts-portal.md) de algumas maneiras:

- **Latência melhorada**: alertas de métrica mais recentes podem ser executados com uma frequência mínima a cada um minuto. Os alertas de métrica antigos são sempre executados em uma frequência de 5 minutos. Alertas do log ainda têm um atraso de mais de 1 minuto devido ao tempo necessário para ingerir os logs. 
- **Suporte para métricas multidimensionais**: é possível alertar sobre métricas dimensionais, permitindo monitorar apenas um segmento interessante da métrica. 
- **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas. Os alertas mais recentes fornecem suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas. 
- **Monitoramento combinado de várias métricas**: é possível monitorar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado. 
- **Melhor sistema de notificação**: todos os alertas mais recentes usam [grupos de ações](monitoring-action-groups.md), que são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas. Alertas de métrica clássicos e alertas antigos do Log Analytics não usam grupos de ações. 
- **Métricas dos Logs** (visualização pública limitada): os dados do log transferidos para o Log Analytics agora podem ser extraídos e convertidos em métricas do Azure Monitor e, em seguida, alertados da mesma forma que outras métricas. 

Para saber como criar um alerta de métrica novo no portal do Azure, consulte [Criar uma regra de alerta no portal do Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal). Após a criação, será possível gerenciar o alerta usando as etapas descritas em [Gerenciar seus alertas no Portal do Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).


## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
Atualmente, é possível criar alertas de métrica novos apenas no portal do Azure ou API REST. O suporte para a configuração de alertas mais recentes usando o PowerShell e a interface de linha de comando do Azure (Azure CLI 2.0) estará disponível em breve.

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Os alertas de métrica mais recentes fornecem suporte a alertas para métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas do [Azure Monitor – Metrics Explorer (versão prévia)](monitoring-metric-charts.md).

Esta é a lista completa das origens métricas do Azure Monitor com suporte pelos alertas mais recentes:

|Tipo de recurso  |Dimensões compatíveis  | Métricas disponíveis|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | sim        | [Gerenciamento da API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     sim   | [Contas de Automação](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Contas do Lote](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Cache Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Máquinas virtuais](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Conjuntos de escala de Máquina Virtual](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   sim     |[Data Factories V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[Banco de dados para MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [Banco de dados para PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  sim      |[Hubs de Evento](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     N/D    |[Aplicativos Lógicos](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Gateways do Aplicativo](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Endereços IP públicos.](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   N/D      |[Serviços de pesquisa](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  sim       |[Barramento de Serviço](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    sim     | [Contas de Armazenamento](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     sim    | [Serviços de blob](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Serviços de arquivo](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [Serviços de fila](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de tabela](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Serviços Cognitivos](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (Versão prévia) | sim|[Espaços de trabalho do Log Analytics](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Registros do Log Analytics como métricas para alertas 

Também é possível usar alertas de métrica mais recentes em logs do Log Analytics populares extraídos como métricas como parte da Versão Prévia das Métricas de Logs.  
- [Contadores de desempenho](../log-analytics/log-analytics-data-sources-performance-counters.md) para computadores Windows e Linux
- [Registros de pulsação para Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md)
- Registros de [gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md)
 
> [!NOTE]
> A métrica e/ou dimensão específica só será exibida se os respectivos dados existirem no período escolhido. Essas métricas estão disponíveis para os clientes com espaços de trabalho no Leste dos EUA, Centro-Oeste dos EUA e Europa Ocidental que aceitaram a versão prévia. Se você gostaria de fazer parte dessa versão prévia, inscreva-se pela [pesquisa](https://aka.ms/MetricLogPreview).

Há suporte para a lista de origens métricas baseadas em log do Log Analytics a seguir:

Nome/detalhes das métricas  |Dimensões compatíveis  | Tipo de log  |
|---------|---------|---------|
|Average_Avg. de segundos/Leitura do Disco     |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Avg. de segundos/Gravação do Disco     |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Current Disk Queue Length   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Disk Reads/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Disk Transfers/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|   Average_% Free Space    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Available MBytes     |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_% Committed Bytes In Use    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
| Average_Bytes Received/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|  Average_Bytes Sent/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|  Average_Bytes Total/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|  Average_% Processor Time    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|   Average_Processor Queue Length    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Windows      |
|   Average_% Free Inodes   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Free Space   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Used Inodes  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Used Space   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Disk Read Bytes/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Disk Reads/sec |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Disk Transfers/sec |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Disk Write Bytes/sec   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Disk Writes/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Free Megabytes |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Logical Disk Bytes/sec |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Available Memory |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Available Swap Space |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Used Memory  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Used Swap Space  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Available MBytes Memory    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Available MBytes Swap  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Page Reads/sec |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Page Writes/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Pages/sec  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Used MBytes Swap Space |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Used Memory MBytes |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Bytes Transmitted    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Bytes Received   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Bytes    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Packets Transmitted  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Packets Received |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Rx Errors    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Tx Errors    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Total Collisions   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Avg. de segundos/Leitura do Disco |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Avg. de segundos/Transferência do Disco |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Avg. de segundos/Gravação do Disco    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Physical Disk Bytes/sec    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Pct Privileged Time    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Pct User Time  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Used Memory kBytes |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Virtual Shared Memory  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% DPC Time |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Idle Time    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Interrupt Time   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% IO Wait Time |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Nice Time    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Privileged Time  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% Processor Time   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_% User Time    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Free Physical Memory   |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Free Space in Paging Files |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Free Virtual Memory    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Processes  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Size Stored In Paging Files    |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Uptime |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Average_Users  |     Sim – Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Contador de desempenho do Linux      |
|    Pulsação  |     Sim – Computador, OSType, Versão & SourceComputerId    |   Registros de pulsação |
|    Atualizar |     Sim – Computer, Product, Classification, UpdateState, Optional & Approved    |   Gerenciamento de atualizações |



## <a name="payload-schema"></a>Esquema de conteúdo

A operação POST contém a carga JSON e esquema a seguir para todos os alertas de métrica mais recentes quando o [grupo de ações ](monitoring-action-groups.md) adequadamente configurado é usado:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a nova [Experiência de alertas](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [alertas de log no Azure](monitor-alerts-unified-log.md).
* Saiba mais sobre os [Alertas no Azure](monitoring-overview-alerts.md).
