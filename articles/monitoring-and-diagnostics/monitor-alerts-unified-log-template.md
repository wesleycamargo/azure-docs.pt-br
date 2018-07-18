---
title: Criar um alerta de log com um modelo do Resource Manager
description: Saiba como criar um log de alertas usando um modelo do Azure Resource Manager e API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5afa34a5eadf5367b3ab28749735197ca6ed82bd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263194"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Criar um alerta de log com um modelo do Resource Manager
Este artigo mostra como você pode gerenciar [registrar alertas](monitor-alerts-unified-log.md) programaticamente em escala, no Azure usando [modelo do Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) via [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) e [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). No momento, Alertas do Azure dá suporte a alertas de log em consultas do [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) e do [Application Insights do Azure](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Gerenciando o alerta de log no Log Analytics
O alerta de log para o [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) é integrado à [novas experiência de alertas do Microsoft Azure](monitoring-overview-unified-alerts.md); enquanto ainda ultrapassa as APIs do Log Analytics e permanece a compatibilidade com o esquema usado anteriormente para gerenciar os [alertas no portal OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> A partir de 14 de maio de 2018, todos os alertas em um espaço de trabalho começarão a ser automaticamente estendidos para o Azure. Um usuário pode começar voluntariamente a estender os alertas para o Azure antes de 14 de maio de 2018. Para obter mais informações, consulte [Estender alertas do OMS ao Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
Os alertas para o Log Analytics são criados por regras de alerta que executam uma pesquisa salva em um intervalo regular. Se os resultados da consulta correspondência aos critérios especificados, será criado um registro de alerta e uma ou mais ações são executadas. 

Modelo de recurso para a pesquisa [do Log analytics salva](../log-analytics/log-analytics-log-searches.md) e [alertas da análise de Log](../log-analytics/log-analytics-alerts.md) estão disponíveis na seção Log Analytics da documentação. Saiba mais sobre [adicionando as pesquisas e alertas do Log Analytics salvos](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); que inclui exemplos ilustrativos, bem como os detalhes do esquema.

### <a name="using-resource-template-via-apipowershell"></a>Usando o Modelo de Recursos por meio da API/Powershell
A API REST Alerta do Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. A API pode então ser acessada da uma linha de comando do PowerShell e produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados de diferentes maneiras por meio de programação.

Saiba mais sobre [criar e gerenciar regras de alerta no Log Analytics com API REST](../log-analytics/log-analytics-api-alerts.md), inclusive exemplos de como acessar a API do Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Gerenciar alerta de log no Application Insights
Os alertas de log para o Application Insights do Azure foram introduzidos como parte dos novos alertas do Microsoft Azure no Azure Monitor. Portanto, é executado sob a API do Azure Monitor como o grupo operacional REST de [Regras de Consulta Agendada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

### <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
O alerta de log para os recursos do Application Insights tem um tipo de `Microsoft.Insights/scheduledQueryRules/`. Para obter mais informações sobre esse tipo de recurso, consulte [Azure Monitor - referência da API de regras de consulta agendada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Esta é a estrutura para [criação de Regras de Consulta Agendada](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) com base em modelo de recursos, com o conjunto de dados de exemplo como variáveis.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Campo de marca com link oculto para o recurso de destino é obrigatório no uso de chamada API para [Regras de Consulta Agendada ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) ou recurso de modelo. 

O json de exemplo acima podem ser salvo como (digamos) sampleScheduledQueryRule.json para fins deste passo a passo e pode ser implantado usando o [Azure Resource Manager no portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Usando o Modelo de Recursos por meio e CLI/Powershell
Azure Monitor - a API de Consulta Programada é uma API REST e totalmente compatível com a API REST do Gerenciador de Recursos do Azure. Portanto, pode ser usado por meio do Powershell usando o cmdlet do Gerenciador de Recursos, bem como a CLI do Azure.

Ilustrado abaixo o uso por meio do cmdlet do PowerShell do Azure Resource Manager para o modelo de recurso mostrado anteriormente (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Ilustrado abaixo o uso por meio do comando do Azure Resource Manager na CLI do Azure para o modelo de recurso mostrado anteriormente (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Operação bem-sucedida, 201 será retornado para a criação da regra de alerta de novo estado ou 200 será retornado se uma regra de alerta existente for modificada.


## <a name="next-steps"></a>Próximas etapas
* Entender [Ações de Webhook para alertas de log](monitor-alerts-unified-log-webhook.md)
* Saiba mais sobre os novos [Alertas do Azure](monitoring-overview-unified-alerts.md)
* Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md)
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md).   
