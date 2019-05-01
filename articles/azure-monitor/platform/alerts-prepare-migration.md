---
title: Preparar para a migração de alertas clássicos do Azure Monitor, atualizando seus aplicativos lógicos e runbooks
description: Saiba como modificar seu webhooks, aplicativos lógicos e runbooks para se preparar para migração voluntária.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727492"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar seus aplicativos lógicos e runbooks para a migração clássicas de regras de alerta

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas em julho de 2019. Uma ferramenta de migração está disponível no portal do Azure para clientes que usam regras de alerta clássicas e que queiram disparar a migração em si.

Se você optar por migrar voluntariamente suas regras de alerta clássicas a novas regras de alerta, lembre-se de que há algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como você pode se preparar para a alteração.

## <a name="api-changes"></a>Alterações de API

As APIs que criam e gerenciam as regras de alerta clássicas (`microsoft.insights/alertrules`) são diferentes das APIs que criam e gerenciam os novos alertas de métrica (`microsoft.insights/metricalerts`). Se por meio de programação, você criar e gerencia regras de alerta clássicas hoje, atualize seus scripts de implantação para trabalhar com as novas APIs.

A tabela a seguir é uma referência para as interfaces programáticas para alertas clássicos e novos:

|         |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modelo do Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Novos alertas de métrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações de carga de notificação

O formato de carga de notificação é um pouco diferente entre [regras de alerta clássicas](alerts-webhooks.md) e [novos alertas de métrica](alerts-metric-near-real-time.md#payload-schema). Se você tiver qualquer webhook, aplicativo lógico ou ações de runbook que são disparadas por regras de alerta clássicas, você deve atualizar os pontos de extremidade de notificação para aceitar o formato de carga de novos alertas de métrica.

Use a tabela a seguir para mapear os campos de carga de webhook do formato do clássico para o novo formato:

|  |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|O alerta foi ativado ou está resolvido?    | **status**       | **data.status** |
|Informações contextuais sobre o alerta     | **contextoo**        | **data.context**        |
|Carimbo de data / hora em que o alerta foi ativado ou resolvido     | **context.timestamp**       | **data.context.timestamp**        |
| ID da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **context.description** | **data.context.description** |
| Condição de regra de alerta | **context.condition** | **data.context.condition** |
| Nome da métrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregação de tempo (como a métrica é agregada no decorrer de janela de avaliação)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Período de avaliação | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operador (como o valor da métrica agregado é comparado com o limite) | **context.condition.operator** | **data.context.condition.operator** |
| Limite | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valor de métrica | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID da assinatura | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupo de recursos do recurso afetado | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome do recurso afetado | **context.resourceName** | **data.context.resourceName** |
| Tipo do recurso afetado | **context.resourceType** | **data.context.resourceType** |
| ID do recurso do recurso afetado | **context.resourceId** | **data.context.resourceId** |
| Link direto para a página de resumo de recursos do portal | **context.portalLink** | **data.context.portalLink** |
| Campos de carga personalizada a ser passado para o webhook ou um aplicativo lógico | **properties** | **data.properties** |

As cargas são semelhantes, como você pode ver. A seção a seguir oferece:

- Detalhes sobre como modificar os aplicativos lógicos para trabalhar com o novo formato.
- Um exemplo de runbook que analisa a carga de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificar um aplicativo lógico para receber notificações de alerta métrica

Se você estiver usando aplicativos lógicos com alertas clássicos, você deve modificar seu código de aplicativo lógico para analisar o novo conteúdo de alertas de métrica. Siga estas etapas:

1. Crie um novo aplicativo lógico.

1. Use o modelo "Azure Monitor – métricas alerta manipulador". Este modelo tem um **solicitação HTTP** gatilho com o esquema apropriado definido.

    ![o modelo de aplicativo lógico](media/alerts-migration/logic-app-template.png "modelo de alerta de métrica")

1. Adicione uma ação para hospedar sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usar um runbook de automação que recebe uma notificação de alerta de métrica

O exemplo a seguir fornece o código do PowerShell para usar em seu runbook. Esse código pode analisar as cargas para regras de alerta de métrica clássicas e novas regras de alerta de métrica.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Para obter um exemplo completo de um runbook que uma máquina virtual é interrompido quando um alerta é disparado, consulte o [documentação da automação do Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros por meio de webhooks

A maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já dão suporte a alertas de métrica mais recentes por meio de suas integrações. Integrações conhecidas que já trabalha com novos alertas de métrica são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se você estiver usando uma integração de parceiro não estiver listada aqui, confirme com o provedor de integração que a integração funciona com novos alertas de métrica.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Entender como funciona a ferramenta de migração](alerts-understand-migration.md)
