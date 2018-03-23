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
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de métrica quase em tempo real (versão prévia)
O Azure Monitor é compatível com um novo tipo de alerta chamado alertas de métrica quase em tempo real (versão prévia). Atualmente, esse recurso está em visualização pública.

Os alertas em tempo real diferem dos alertas de métrica comuns de algumas maneiras:

- **Melhor latência**: os alertas de métrica quase em tempo real podem monitorar alterações nos valores de métrica com uma frequência de até um minuto.
- **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas em alertas de métrica quase em tempo real. Os alertas dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Métricas de logs**: as métricas podem ser extraídas dos dados de log populares que chegam ao [Log Analytics](../log-analytics/log-analytics-overview.md) para o Azure Monitor e ser gerado um alerta quase em tempo real
- **Monitoramento combinado de várias métricas**: os alertas de métrica quase em tempo real podem monitorar várias métricas (atualmente, até duas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
- **Sistema de notificação modular**: os alertas de métrica quase em tempo real usam [grupos de ação](monitoring-action-groups.md). É possível usar grupos de ação para criar ações modulares. É possível reutilizar grupos de ação para várias regras de alerta.

> [!NOTE]
> Atualmente, o alerta de métrica quase em tempo real está em visualização pública. As métricas dos recursos de logs estão em visualização pública *limitada*. A funcionalidade e a experiência do usuário está sujeita a alterações.
>

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Alertas de métrica quase em tempo real dão suporte a métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas do [Azure Monitor – Metrics Explorer (versão prévia)](monitoring-metric-charts.md).

Eis a lista completa dos tipos de recursos do Azure Monitor compatíveis com alertas de métrica quase em tempo real:

|Nome/detalhes das métricas  |Dimensões compatíveis  |
|---------|---------|
|Microsoft.ApiManagement/service     | sim        |
|Microsoft.Automation/automationAccounts     |     N/D    |
|Microsoft.Automation/automationAccounts     |   N/D      |
|Microsoft.Cache/Redis     |    N/D     |
|Microsoft.Compute/virtualMachines     |    N/D     |
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |
|Microsoft.DataFactory/factories     |   N/D      |
|Microsoft.DBforMySQL/servers     |   N/D      |
|Microsoft.DBforPostgreSQL/servers     |    N/D     |
|Microsoft.EventHub/namespaces     |   N/D      |
|Microsoft.Logic/workflows     |     N/D    |
|Microsoft.Network/applicationGateways     |    N/D     |
|Microsoft.Network/publicipaddresses     |  N/D       |
|Microsoft.Search/searchServices     |   N/D      |
|Microsoft.ServiceBus/namespaces     |  N/D       |
|Microsoft.Storage/storageAccounts     |    sim     |
|Microsoft.Storage/storageAccounts/services     |     sim    |
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       |
|Microsoft.CognitiveServices/accounts     |    N/D     |


Atualmente, as métricas de logs são compatíveis com os seguintes logs populares do OMS:
- [Contadores de desempenho](../log-analytics/log-analytics-data-sources-performance-counters.md) para computadores Windows e Linux
- Registros de pulsação para computadores
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
> A métrica e/ou dimensão específica só será exibida se os respectivos dados existirem no período escolhido

## <a name="create-a-near-real-time-metric-alert"></a>Criar um alerta de métrica quase em tempo real
Atualmente, é possível criar alertas de métrica quase em tempo real apenas no portal do Azure. O suporte para a configuração de alertas de métrica quase em tempo real usando PowerShell, a CLI (interface de linha de comando) do Azure e APIs REST do Azure Monitor estará disponível em breve.

A experiência de criação de alertas quase em tempo real mudou para a nova página de **Alertas (versão prévia)**. Embora a página de alertas atual mostre **Adicionar Alerta de Métrica Quase em Tempo Real**, você é redirecionado para a página **Alertas (versão prévia)**.

Para saber como criar um alerta quase em tempo real, consulte [Criar uma regra de alerta no portal do Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gerenciar alertas de métrica quase em tempo real
Depois de criar um alerta de métrica quase em tempo real, você pode gerenciá-lo usando as etapas descritas em [Gerenciar seus alertas no portal do Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

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

* [Saiba mais sobre a nova experiência de Alertas (versão prévia)](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure (versão prévia)](monitor-alerts-unified-log.md).
* Saiba mais sobre os [Alertas no Azure](monitoring-overview-alerts.md).
