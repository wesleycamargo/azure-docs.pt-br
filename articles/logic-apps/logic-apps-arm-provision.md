---
title: "Criar um aplicativo lógico usando um modelo no Azure | Microsoft Docs"
description: "Use um modelo do Azure Resource Manager para implantar um aplicativo lógico para definir fluxos de trabalho."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 1305fdc4cf87905eef701ec5471d6329006c2b5f
ms.contentlocale: pt-br
ms.lasthandoff: 01/31/2017


---
# <a name="create-a-logic-app-using-a-template"></a>Crie um Aplicativo Lógico usando um modelo
Os modelos fornecem uma maneira rápida de usar conectores diferentes dentro de um aplicativo lógico. Os aplicativos lógicos incluem modelos do Azure Resource Manager para criar um aplicativo lógico que pode ser usado para definir fluxos de trabalho comerciais. Você pode definir quais recursos são implantados e como definir os parâmetros quando implantar seu aplicativo lógico. Você pode usar esse modelo para seus próprios cenários de negócios ou personalizá-lo para atender às suas necessidades.

Para obter mais detalhes sobre as propriedades do Aplicativo lógico, consulte [API de Gerenciamento de Fluxo de Trabalho de Aplicativo Lógico](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obter exemplos da definição em si, consulte [Criar definições de Aplicativos Lógicos](logic-apps-author-definitions.md). 

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para o modelo completo, consulte [Modelo de Aplicativo Lógico](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-deploy"></a>O que você implanta
Neste modelo, você implanta um aplicativo lógico.

Para executar a implantação automaticamente, selecione o seguinte botão:  

[![Implantar no Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>Recursos a implantar
### <a name="logic-app"></a>Aplicativo lógico
Cria o aplicativo lógico.

Os modelos usam um valor de parâmetro para o nome do aplicativo lógico. Ele define o local do aplicativo lógico para o mesmo local que o grupo de recursos. 

Essa definição específica é executada uma vez por hora e executa ping do local especificado no parâmetro **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI do Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup




