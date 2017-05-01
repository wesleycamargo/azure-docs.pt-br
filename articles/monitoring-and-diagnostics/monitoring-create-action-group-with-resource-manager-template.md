---
title: "Criar grupos de ação com modelos do Resource Manager | Microsoft Docs"
description: "Os grupos de ações permitem notificar email, SMS ou chamar webhooks quando determinados eventos ocorrem."
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
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 2dd7b14f1466fa7244a2af2c030d8b794658aaad
ms.lasthandoff: 04/12/2017


---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Criar um grupo de ação com um modelo do Resource Manager
Este artigo mostra como você pode usar um [modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para configurar grupos de ação. Os modelos permitem configurar grupos de ação nos recursos automaticamente quando eles são criados para garantir que todas as partes corretas são notificadas quando um alerta é disparado.

Estas são as etapas básicas:

1.    Crie um modelo como um arquivo JSON que descreve como criar o grupo de ação.
2.    [Implantar o modelo usando qualquer método de implantação.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

Abaixo, descrevemos como criar um modelo do Resource Manager pela primeira vez para um grupo de ação apenas e, em seguida, para um grupo de ação durante a criação de outro recurso.

## <a name="resource-manager-template-for-an-action-group"></a>Modelo do Resource Manager para um grupo de ação

Para criar um grupo de ação usando um modelo do Resource Manager, você cria um recurso do tipo `Microsoft.Insights/actionGroups` e preenche todas as propriedades relacionadas. Veja a seguir alguns modelos de exemplo que criam um grupo de ação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)  
Saiba mais sobre [Alertas](monitoring-overview-alerts.md)  
Como adicionar [Alertas usando um modelo do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

