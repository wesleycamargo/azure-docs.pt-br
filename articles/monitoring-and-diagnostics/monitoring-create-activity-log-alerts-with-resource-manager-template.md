---
title: Criar um alerta do log de atividades com um modelo do Resource Manager
description: Seja notificado quando seus recursos do Azure forem criados.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262983"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Criar um alerta do log de atividades com um modelo do Resource Manager
Este artigo mostra como usar um [modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para configurar alertas do log de atividades. Usando modelos, você pode facilmente configurar vários alertas que são ativados com base em condições específicas de evento de log de atividades como parte do processo de implantação automática.

As etapas básicas são:

1. Crie um modelo como um arquivo JSON que descreve como criar o alerta do log de atividades.

2. Implantar o modelo usando [qualquer método de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modelo do Resource Manager para um alerta do log de atividades
Para criar um alerta do log de atividades usando um modelo do Resource Manager, você cria um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, você preencherá todas as propriedades relacionadas. Veja abaixo um modelo que cria um alerta do log de atividades.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Visite nossa [Galeria de Início Rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) para obter alguns exemplos de modelos de alerta do log de atividades.

> [!NOTE]

> Você também pode regras de alerta de log de atividade usando a experiência de usuário aprimorada no Monitor > [Alertas (versão prévia)](monitoring-overview-unified-alerts.md). Para obter mais informações sobre como criá-los, consulte [este artigo](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [alertas](monitoring-overview-alerts.md).
- Saiba como adicionar [grupos de ações usando um modelo do Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- Saiba como [criar um alerta do log de atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Saiba como [criar um alerta do log de atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
