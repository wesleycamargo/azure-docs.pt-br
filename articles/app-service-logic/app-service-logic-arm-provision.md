<properties 
	pageTitle="Criar um aplicativo lógico" 
	description="Use um modelo do Gerenciador de Recursos do Azure para implantar um Aplicativo Lógico vazio para definir fluxos de trabalho." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Crie um Aplicativo Lógico usando um modelo

Neste tópico, você aprenderá como criar um modelo do Gerenciador de Recursos do Azure para criar um aplicativo lógico vazio que pode ser usado para definir os fluxos de trabalho. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais detalhes sobre as propriedades do Aplicativo lógico, consulte [API de Gerenciamento de Fluxo de Trabalho de Aplicativo Lógico](https://msdn.microsoft.com/library/azure/dn948513.aspx). Para obter exemplos da definição em si, consulte [Criar definições de Aplicativos Lógicos](app-service-logic-author-definitions.md).

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para o modelo completo, consulte [Modelo de Aplicativo Lógico](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicApp.json).

## O que você implantará

Neste modelo, você implantará um aplicativo lógico.

## Parâmetros

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Recursos a implantar

### Plano do serviço de aplicativo

Cria um plano de serviço de aplicativo.

Ele usa o mesmo local que o grupo de recursos no qual ele está sendo implantado.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### Aplicativo lógico

Cria o aplicativo lógico.

Os modelos usam um valor de parâmetro para o nome do aplicativo lógico. Ele define o local do aplicativo lógico para o mesmo local que o grupo de recursos.

Essa definição específica será executada uma vez por hora e executará ping do local especificado no parâmetro **testUri**.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -ResourceGroupName ExampleDeployGroup

### CLI do Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -g ExampleDeployGroup


 

<!---HONumber=62-->