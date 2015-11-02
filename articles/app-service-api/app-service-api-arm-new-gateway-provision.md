<properties 
	pageTitle="Implantar um aplicativo de API com um novo gateway" 
	description="Use um modelo do Gerenciador de Recursos do Azure para implantar um aplicativo de API com um novo gateway e um novo plano de Serviço de Aplicativo." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="tomfitz"/>

# Provisionar um aplicativo de API com um novo gateway

Neste tópico, você aprenderá como criar um modelo do Gerenciador de Recursos do Azure que implanta um aplicativo de API do Azure e um gateway. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para obter mais informações sobre a implantação de aplicativos, consulte [Implantar um aplicativo complexo de maneira previsível no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Para o modelo completo, consulte [Aplicativo de API com novo modelo de gateway](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-new/azuredeploy.json).

## O que você implantará

Neste modelo, você implantará:

- Aplicativo de API
- novo gateway
- novo plano de hospedagem do Serviço de Aplicativo

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-new%2Fazuredeploy.json)

## Parâmetros

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanName

O nome do Plano de Serviço do Aplicativo.

    "hostingPlanName": {
      "type": "string"
    }

### hostingPlanSettings

As configurações para o novo plano de hospedagem.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "computeMode": "Dedicated",
        "siteMode": "Limited",
        "sku": "Standard",
        "workerSize": "0",
        "hostingEnvironment": ""
      }
    }
    
## Variáveis

Esse modelo define uma variável que é usada ao implantar os recursos.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
O valor é usado abaixo como **variables('packageId')**. Ele contém a ID do pacote do NuGet para aplicativos da API.

## Recursos a implantar

### Plano de hospedagem

Cria o plano de hospedagem de serviço para o aplicativo de API.

    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2015-04-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('hostingPlanSettings').sku]",
        "workerSize": "[parameters('hostingPlanSettings').workerSize]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "numberOfWorkers": 1
      }
    }

### Aplicativo Web que hospeda o gateway

Cria um aplicativo Web que hospeda o gateway.

Observe que o parâmetro **kind** (tipo) é definido como **gateway**, que notifica o portal do Azure que esse aplicativo Web está hospedando um gateway. O portal ocultará o aplicativo Web da folha Procurar aplicativos Web. Um link é definido entre o aplicativo Web que hospeda e o gateway. A seção de configurações do aplicativo inclui os valores necessários para hospedar o aplicativo de API. O **serverFarmId**contém o nome do plano de serviço do aplicativo que você forneceu no parâmetro **hostingPlanName**.


    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('gatewayName')]",
      "location": "[parameters('location')]",
      "kind": "gateway",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/gateway",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('gatewayName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "ApiAppsGateway_EXTENSION_VERSION",
              "value": "latest"
            },
            {
              "name": "EmaStorage",
              "value": "D:\\home\\data\\apiapps"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ]
    }

### Gateway

Cria o gateway.

O aplicativo Web responsável pela hospedagem é definido como uma propriedade do gateway.

    {
      "type": "Microsoft.AppService/gateways",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('gatewayName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/gatewaySite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
      ],
      "properties": {
        "host": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }

### Aplicativo Web que hospeda o aplicativo de API

Cria um aplicativo Web que hospeda o aplicativo de API.

Observe que o parâmetro **kind** (tipo) é definido como **apiApp**, que notifica o portal do Azure que esse aplicativo Web está hospedando um aplicativo de API. O portal ocultará o aplicativo Web da folha Procurar aplicativos Web. O aplicativo inclui uma extensão para instalar o pacote do aplicativo de API vazio padrão. Um link é definido entre o aplicativo de API e o aplicativo da Web que o hospeda. A seção de configurações do aplicativo inclui os valores necessários para hospedar o aplicativo de API. O **serverFarmId**contém o nome do plano de serviço do aplicativo que você forneceu no parâmetro **hostingPlanName**.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('gatewayName'))).hostNames[0])]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
      ]
    }

### Aplicativo de API

Cria o aplicativo de API.

Observe que os nomes do aplicativo Web responsável pela hospedagem e do gateway são definidos como propriedades no aplicativo de API.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]",
        "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-new/azuredeploy.json

### CLI do Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-new/azuredeploy.json


 

<!---HONumber=Oct15_HO4-->