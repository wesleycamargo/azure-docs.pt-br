---
title: Alertas de métrica quase em tempo real no Azure Monitor | Microsoft Docs
description: Saiba como usar alertas de métrica quase em tempo real para monitorar métricas de recursos do Azure com frequência de até 1 minuto.
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
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Use os alertas de métrica mais novos para serviços do Azure no portal do Azure
O Azure Monitor é compatível com um novo tipo de alerta chamado alertas de métrica quase em tempo real. 

Os alertas em tempo real diferem dos [alertas de métrica clássicos](insights-alerts-portal.md) de algumas maneiras:

- **Latência melhorada**: alertas de métrica quase em tempo real podem ser executados com uma frequência mínima de a cada um minuto. Os alertas de métrica antigos são sempre executados em uma frequência de 5 minutos.
- **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais, permitindo que você monitore um segmento interessante da métrica.
- **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas em alertas de métrica quase em tempo real. Os alertas dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Monitoramento combinado de várias métricas**: os alertas de métrica quase em tempo real podem monitorar várias métricas (atualmente, até duas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
- **Sistema de notificação modular**: os alertas de métrica quase em tempo real usam [grupos de ação](monitoring-action-groups.md). É possível usar grupos de ação para criar ações modulares. É possível reutilizar grupos de ação para várias regras de alerta.
- **Métricas de logs**: as métricas podem ser extraídas dos dados de log populares que chegam ao [Log Analytics](../log-analytics/log-analytics-overview.md) para o Azure Monitor e ser gerado um alerta quase em tempo real.


## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Alertas de métrica quase em tempo real dão suporte a métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas do [Azure Monitor – Metrics Explorer (versão prévia)](monitoring-metric-charts.md).

Eis a lista completa dos tipos de recursos do Azure Monitor compatíveis com alertas de métrica quase em tempo real:

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
|Microsoft.OperationalInsights/workspaces (Versão prévia) | sim|[Espaços de trabalho do Log Analytics](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Criar um alerta de métrica novo
Atualmente, é possível criar alertas de métrica novos apenas no portal do Azure ou API REST. O suporte para a configuração de alertas de métrica quase em tempo real usando PowerShell, a CLI (interface de linha de comando) do Azure estará disponível em breve.

Para saber como criar um alerta de métrica novo no portal do Azure, consulte [Criar uma regra de alerta no portal do Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Gerenciar alertas de métrica novos
Depois de criar um alerta de métrica quase em tempo real, você pode gerenciá-lo usando as etapas descritas em [Gerenciar seus alertas no portal do Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Suporte para logs do OMS como métricas para alertas

Você também pode usar alertas de métrica quase em tempo real nos logs do OMS populares extraídos como métricas como parte da versão prévia de Métricas de Logs.  
- [Contadores de desempenho](../log-analytics/log-analytics-data-sources-performance-counters.md) para computadores Windows e Linux
- [Registros de pulsação para Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md)
- Registros de [gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md)

Eis a lista completa dos tipos de recursos do OMS compatíveis com alertas de métrica quase em tempo real:

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

> [!NOTE]
> A métrica e/ou dimensão específica só será exibida se os respectivos dados existirem no período escolhido. Essas métricas estão disponíveis para os clientes com espaços de trabalho no Leste dos EUA, Centro-Oeste dos EUA e Europa Ocidental que aceitaram a versão prévia. Se você gostaria de fazer parte dessa versão prévia, inscreva-se pela [pesquisa](https://aka.ms/MetricLogPreview).


## <a name="payload-schema"></a>Esquema de conteúdo

A operação POST contém a carga JSON e esquema a seguir para todos os alertas de métrica quase em tempo real quando o [grupo de ação ](monitoring-action-groups.md) adequadamente configurado é usado:

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
