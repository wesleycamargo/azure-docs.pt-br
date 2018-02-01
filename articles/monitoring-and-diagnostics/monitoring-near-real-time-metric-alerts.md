---
title: "Alertas de Métrica Quase em Tempo Real no Azure Monitor | Microsoft Docs"
description: "Os alertas de métrica quase em tempo real permitem monitorar as métricas de recurso do Azure com uma frequência de até 1 minuto."
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
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de Métrica Quase em Tempo Real (Versão Prévia)
Agora, o Azure Monitor dá suporte a um novo tipo de alertas de métrica chamado Alertas de Métrica Quase em Tempo Real (Versão Prévia). Esse recurso está atualmente em visualização pública.
Esses alertas diferem dos alertas de métrica comuns de algumas maneiras

- **Melhor Latência** – os alertas de métrica quase em tempo real podem monitorar as alterações nos valores de métrica com uma frequência de até 1 minuto.
- **Mais controle sobre as condições de métrica** – os alertas de métrica quase em tempo real permitem aos usuários definir regras de alerta mais avançadas. Os alertas agora dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Monitoramento combinado de várias métricas** – os alertas de métrica quase em tempo real podem monitorar várias métricas (atualmente duas) com uma única regra. O alerta é disparado se ambas as métricas violam seus respectivos limites para o período especificado.
- **Sistema de notificação modular** – os alertas de métrica quase em tempo real usam [grupos de ação](monitoring-action-groups.md). Essa funcionalidade permite aos usuários criar ações de forma modular e reutilizá-las para muitas regras de alerta.

> [!NOTE]
> Atualmente, o recurso de alertas de métrica quase em tempo real está em visualização pública. A funcionalidade e a experiência do usuário está sujeita a alterações.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Para quais recursos é possível criar alertas de métrica quase em tempo real?
Lista completa dos tipos de recursos com suporte nos alertas de métrica quase em tempo real:

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

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Alertas de Métrica Quase em Tempo Real sobre métricas com dimensões
Alertas de Métrica Quase em Tempo Real oferece suporte para o alerta sobre métricas com dimensões. Dimensões são uma forma de filtrar sua métrica para o nível certo. Alertas de métrica quase em tempo real em métricas com dimensões têm suporte para os seguintes tipos de recurso

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (com suporte somente para contas de armazenamento nas regiões dos EUA)
* Microsoft.Storage/storageAccounts/services (com suporte somente para contas de armazenamento nas regiões dos EUA)


## <a name="create-a-near-real-time-metric-alert"></a>Criar um alerta de métrica quase em tempo real
Atualmente, os alertas de métrica quase em tempo real podem ser criados apenas por meio do portal do Azure. O suporte à configuração de alertas de métrica quase em tempo real por meio do PowerShell, da CLI (interface de linha de comando) e da API REST do Azure Monitor estará disponível em breve.

A experiência de criação de alerta do Alertas de Métrica Quase em Tempo Real mudou para a nova experiência **Alertas (Versão prévia)**. Apesar da página Alertas atual mostrar **Adicionar alerta de Métrica Quase em Tempo Real**, você será redirecionado para a nova experiência.

Você pode criar um alerta de métrica quase em tempo real usando as etapas descritas [aqui](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Gerenciando alertas de métrica quase em tempo real
Após a criação de um **alerta de Métrica Quase em Tempo Real**, ele poderá ser gerenciado usando as etapas descritas [aqui](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Esquema de conteúdo

A operação POST contém o seguinte esquema e conteúdo JSON para todos os alertas de métricas quase em tempo real.

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

* [Saiba mais sobre a nova experiência de Alertas (versão prévia)](monitoring-overview-unified-alerts.md)
* [Saiba mais sobre os Alertas de Log no Alertas do Azure (versão prévia)](monitor-alerts-unified-log.md)
* [Saiba mais sobre os Alertas no Azure](monitoring-overview-alerts.md)