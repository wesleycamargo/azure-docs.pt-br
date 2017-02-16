---
title: "Criar um aplicativo lógico usando modelos do Azure Resource Manager no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Use um modelo do Azure Resource Manager para implantar um Aplicativo Lógico vazio para definir fluxos de trabalho."
services: logic-apps
documentationcenter: 
author: MSFTMan
manager: erikre
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: a0580f8d303c7ce33a65f0ce6faecf2492f851b0
ms.openlocfilehash: dfe1cddd8a0d2558eecff8123cc5ce6ebaad97e8


---
# <a name="create-a-logic-app-using-a-template"></a>Crie um Aplicativo Lógico usando um modelo
Use um modelo do Azure Resource Manager para criar um aplicativo lógico vazio que possa ser usado para definir os fluxos de trabalho. Você pode definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais detalhes sobre as propriedades do Aplicativo lógico, consulte [API de Gerenciamento de Fluxo de Trabalho de Aplicativo Lógico](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obter exemplos da definição em si, consulte [Criar definições de Aplicativos Lógicos](app-service-logic-author-definitions.md). 

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para o modelo completo, consulte [Modelo de Aplicativo Lógico](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>O que você implantará
Neste modelo, você implanta um aplicativo lógico.

Para executar a implantação automaticamente, selecione o seguinte botão:  

[![Implantar no Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

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






<!--HONumber=Dec16_HO3-->


