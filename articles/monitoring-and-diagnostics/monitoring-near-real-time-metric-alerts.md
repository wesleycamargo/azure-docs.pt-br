---
title: "Alertas de métrica quase em tempo real no Azure Monitor | Microsoft Docs"
description: "Saiba como usar alertas de métrica quase em tempo real para monitorar métricas de recursos do Azure com frequência de até 1 minuto."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de métrica quase em tempo real (versão prévia)
O Azure Monitor é compatível com um novo tipo de alerta chamado alertas de métrica quase em tempo real (versão prévia). Atualmente, esse recurso está em visualização pública.

Os alertas em tempo real diferem dos alertas de métrica comuns de algumas maneiras:

- **Melhor latência**: os alertas de métrica quase em tempo real podem monitorar alterações nos valores de métrica com uma frequência de até 1 minuto.
- **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas em alertas de métrica quase em tempo real. Os alertas dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Monitoramento combinado de várias métricas**: os alertas de métrica quase em tempo real podem monitorar várias métricas (atualmente, até duas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
- **Sistema de notificação modular**: os alertas de métrica quase em tempo real usam [grupos de ação](monitoring-action-groups.md). É possível usar grupos de ação para criar ações modulares. É possível reutilizar grupos de ação para várias regras de alerta.

> [!NOTE]
> Atualmente, o recurso de alerta de métrica quase em tempo real está em visualização pública. A funcionalidade e a experiência do usuário está sujeita a alterações.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Recursos que você pode usar com alertas de métrica quase em tempo real
Veja a lista completa dos tipos de recursos com suporte para alertas de métrica quase em tempo real:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Alertas de métrica quase em tempo real sobre métricas que usam dimensões
Alertas de métrica quase em tempo real dão suporte a métricas que usam dimensões. É possível usar dimensões para filtrar sua métrica para o nível certo. Alertas de métrica quase em tempo real para métricas que usam dimensões têm suporte para os seguintes tipos de recurso:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (com suporte apenas para contas de armazenamento em regiões dos EUA)
* Microsoft.Storage/storageAccounts/services (com suporte apenas para contas de armazenamento em regiões dos EUA)

## <a name="create-a-near-real-time-metric-alert"></a>Criar um alerta de métrica quase em tempo real
Atualmente, é possível criar alertas de métrica quase em tempo real apenas no portal do Azure. O suporte para a configuração de alertas de métrica quase em tempo real usando PowerShell, a CLI (interface de linha de comando) do Azure e APIs REST do Azure Monitor estará disponível em breve.

A experiência de criação de alertas quase em tempo real mudou para a nova página de **Alertas (versão prévia)**. Embora a página de alertas atual mostre **Adicionar Alerta de Métrica Quase em Tempo Real**, você é redirecionado para a página **Alertas (versão prévia)**.

Para saber como criar um alerta quase em tempo real, consulte [Criar uma regra de alerta no portal do Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gerenciar alertas de métrica quase em tempo real
Depois de criar um alerta de métrica quase em tempo real, você pode gerenciá-lo usando as etapas descritas em [Gerenciar seus alertas no portal do Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Esquema de conteúdo

A operação POST contém o seguinte esquema e conteúdo JSON para todos os alertas de métricas quase em tempo real:

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a nova experiência de Alertas (versão prévia)](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure (versão prévia)](monitor-alerts-unified-log.md).
* Saiba mais sobre os [Alertas no Azure](monitoring-overview-alerts.md).