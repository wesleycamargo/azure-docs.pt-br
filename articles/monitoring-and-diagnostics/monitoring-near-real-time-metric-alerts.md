---
title: Recursos com suporte para alertas de métrica no Azure Monitor
description: Referência sobre métricas e logs de suporte para alertas de métrica no Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 89b412a58291dd542b38cd0cbfa1288795024151
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613698"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos com suporte para alertas de métrica no Azure Monitor

Agora, o Azure Monitor dá suporte a um [novo tipo de alerta de métrica](monitoring-overview-alerts.md), que tem vantagens significativas sobre os [alertas de métrica clássicos](monitoring-overview-alerts-classic.md) mais antigos. As métricas estão disponíveis para uma [ampla lista de serviços do Azure](monitoring-supported-metrics.md). Os alertas mais recentes dão suporte a um subconjunto (cada vez maior) de tipos de recurso. Este artigo lista esse subconjunto.


Use também os alertas de métrica mais recentes em logs populares do Log Analytics extraídos como métricas. Para saber mais, exiba [Alertas de Métrica para Logs](monitoring-metric-alerts-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
Atualmente, é possível criar alertas de métrica mais novos apenas no portal do Azure, [API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) ou [Modelos do Gerenciador de Recursos](monitoring-create-metric-alerts-with-templates.md). Suporte para configurar os alertas mais recentes usando o PowerShell e a CLI do Azure versões 2.0 e superiores em breve.

## <a name="metrics-and-dimensions-supported"></a>Medidas e dimensões com suporte
Os alertas de métrica mais recentes fornecem suporte a alertas para métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas compatíveis, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas no [Azure Monitor – Metrics Explorer](monitoring-metric-charts.md).

Esta é a lista completa das origens métricas do Azure Monitor com suporte pelos alertas mais recentes:

|Tipo de recurso  |Dimensões compatíveis  | Métricas disponíveis|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | SIM        | [Gerenciamento da API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     SIM   | [Contas de Automação](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| [Contas do Lote](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/D     |[Cache Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/D     | [Serviços Cognitivos](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/D     | [Máquinas virtuais](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/D      |[Conjuntos de dimensionamento de máquinas virtuais](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | SIM| [Grupos de contêineres](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | SIM | [Clusters gerenciados](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| SIM| [Data Factories V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   SIM     |[Data Factories V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/D      |[Banco de dados para MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/D     | [Banco de dados para PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  SIM      |[Hubs de Evento](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Não  | [Vaults](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     N/D    |[Aplicativos Lógicos](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/D     | [Gateways do Aplicativo](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | N/D |  [Circuitos do ExpressRoute](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | N/D| [Zonas DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (apenas para SKUs Standard)| SIM| [Balanceadores de carga](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/D       |[Endereços IP Públicos](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | N/D | [Capacidades](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | SIM | [Perfis do Gerenciador de Tráfego](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   N/D      |[Serviços de pesquisa](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  SIM       |[Barramento de Serviço](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    SIM     | [Contas de Armazenamento](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     SIM    | [Serviços de blob](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Serviços de arquivo](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [Serviços de fila](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de tabela](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/D       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | SIM | [Planos do Serviço de Aplicativo](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | SIM | [Serviços de Aplicativos](monitoring-supported-metrics.md#microsoftwebsites-excluding-functions) e [Funções](monitoring-supported-metrics.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | SIM | [Slots do Serviço de Aplicativo](monitoring-supported-metrics.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| SIM|[Workspaces do Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



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

* Saiba mais sobre a nova [Experiência de alertas](monitoring-overview-alerts.md).
* Saiba mais sobre os [alertas de log no Azure](monitor-alerts-unified-log.md).
* Saiba mais sobre os [Alertas no Azure](monitoring-overview-alerts.md).
