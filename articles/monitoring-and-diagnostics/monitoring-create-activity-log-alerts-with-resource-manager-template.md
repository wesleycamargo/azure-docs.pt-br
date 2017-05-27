---
title: Criar um alerta do log de atividades com um modelo do Resource Manager | Microsoft Docs
description: Seja notificado quando seus recursos do Azure forem criados.
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.contentlocale: pt-br
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Criar um alerta do log de atividades com um modelo do Resource Manager
Este artigo mostra como você pode usar um [modelo do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) para configurar alertas do log de atividades. Isso permite que você configure alertas sobre os recursos automaticamente quando eles são criados como parte do processo de implantação automatizada.

Estas são as etapas básicas:

1.    Crie um modelo como um arquivo JSON que descreve como criar o alerta do log de atividades.
2.    [Implantar o modelo usando qualquer método de implantação.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

Abaixo, descrevemos como criar um modelo do Resource Manager pela primeira vez para um alerta do log de atividades apenas e, em seguida, para um alerta do log de atividades durante a criação de outro recurso.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modelo do Resource Manager para um alerta do log de atividades
Para criar um alerta do log de atividades usando um modelo do Resource Manager, você cria um recurso do tipo `microsoft.insights/activityLogAlerts` e preenche todas as propriedades relacionadas. Veja abaixo um modelo que cria um alerta do log de atividades.

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

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Alertas](monitoring-overview-alerts.md)  
Como adicionar [grupos de ação usando um modelo do Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

