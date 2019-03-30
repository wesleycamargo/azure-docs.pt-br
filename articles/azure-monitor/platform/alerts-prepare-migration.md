---
title: Preparar para a migração de alertas clássicos do Azure Monitor, atualizando seus aplicativos lógicos e runbooks
description: Saiba como modificar seu webhook, aplicativo lógico e runbooks para se preparar para migração voluntária.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631967"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Preparar seus aplicativos lógicos e livros para a migração de regras de alerta clássico de execução

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas em julho de 2019. A ferramenta de migração para disparar voluntariamente a migração está disponível no portal do Azure e está distribuindo para os clientes que usam regras de alerta clássicas.

Se você optar por migrar voluntariamente suas regras de alerta clássicas a novas regras de alerta, há algumas diferenças entre os dois sistemas, você deve estar atento. Este artigo explica as diferenças entre os dois sistemas e como você pode se preparar para a alteração.

## <a name="api-changes"></a>Alterações de API

As APIs usadas para criar/gerenciar regras de alerta clássicas (`microsoft.insights/alertrules`) são diferentes das APIs usadas para criar/gerenciar alertas de métrica novo (`microsoft.insights/metricalerts`). Se você criar/gerenciar programaticamente as regras de alerta clássicas hoje em dia, atualize seus scripts de implantação para trabalhar com novas APIs.

A tabela a seguir fornece uma referência a interfaces programáticas para alertas clássicos e novos.

|         |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referência](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modelo do Azure Resource Manager | [Para alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Novos alertas de métrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Alterações de carga de notificação

O formato de carga de notificação é um pouco diferente entre [regras de alerta clássicas](alerts-webhooks.md) e [novos alertas de métrica](alerts-metric-near-real-time.md#payload-schema). Se você tiver qualquer webhook, aplicativo lógico ou ações de runbook seja disparadas por regras de alerta clássicas, você precisará atualizar os pontos de extremidade de notificação para aceitar o formato de carga de novos alertas de métrica.

Você pode usar a tabela a seguir para mapear os campos entre o conteúdo de webhook de regra de alerta clássico e a nova carga de webhook de alerta de métrica.

|  |Alertas clássicos  |Novos alertas de métrica |
|---------|---------|---------|
|O alerta foi ativado ou está resolvido     | status       | data.status |
|Informações contextuais sobre o alerta     | context        | data.context        |
|Carimbo de hora em que o alerta foi ativado ou resolvido      | context.timestamp       | data.context.timestamp        |
| ID da regra de alerta | context.id | data.context.id |
| Nome da regra de alerta | context.name | data.context.name |
| Descrição da regra de alerta | context.description | data.context.description |
| Condição de regra de alerta | context.condition | data.context.condition|
| Nome da métrica | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Agregação de tempo (como a métrica é agregada no decorrer de janela de avaliação)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Período de avaliação | context.condition.windowSize | data.context.condition.windowSize|
| Operador (como o valor da métrica agregado é comparado com o limite) | context.condition.operator | data.context.condition.operator|
| Limite | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Valor de métrica | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| ID da assinatura | context.subscriptionId | data.context.subscriptionId|
| Grupo de recursos do recurso afetado | context.resourceGroup | data.context.resourceGroup|
| Nome do recurso afetado | context.resourceName | data.context.resourceName |
| Tipo do recurso afetado | context.resourceType | data.context.resourceType |
|  ID do recurso do recurso afetado | context.resourceId | data.context.resourceId |
| Um link direto para a página de resumo de recursos do portal | context.portalLink | data.context.portalLink|
| Campos de carga personalizada a ser passado para o webhook ou um aplicativo lógico | propriedades |data.properties |

Como você pode ver, ambas as cargas são semelhantes. Seção a seguir tem detalhes sobre aplicativos lógicos de exemplo e um exemplo de runbook para analisar a carga de notificação para novos alertas.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Usando um aplicativo lógico que recebe uma notificação de alerta de métrica

Se você estiver usando aplicativos lógicos com alertas clássicos, você precisará modificar seu aplicativo lógico para analisar o novo conteúdo de alertas de métrica.

1. Crie um novo aplicativo lógico.

2. Use o modelo "Azure Monitor – métricas alerta manipulador". Este modelo tem um **solicitação HTTP** gatilho com o esquema apropriado definido

    ![o modelo de aplicativo lógico](media/alerts-migration/logic-app-template.png "modelo de alerta de métrica")

3. Adicione uma ação para hospedar sua lógica de processamento.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usando um runbook de automação que recebe uma notificação de alerta de métrica

O exemplo a seguir fornece o código do PowerShell que pode ser usado em seu runbook que possa analisar as cargas para regras de alerta de métrica clássicas e novas regras de alerta de métrica.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

Veja um exemplo completo de um runbook que uma VM é interrompido quando um alerta é disparado na [documentação da automação do Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros por meio de webhooks

A maioria dos [nossos parceiros que se integram com alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/partners) já dão suporte a alertas de métrica mais recentes por meio de suas integrações. Integrações conhecidas que já trabalha com novos alertas de métrica estão listadas abaixo.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se você estiver usando uma integração de parceiro não estiver listada aqui, confirme com o provedor de integração que a integração funciona com novos alertas de métrica.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Entender como funciona a ferramenta de migração](alerts-understand-migration.md)
