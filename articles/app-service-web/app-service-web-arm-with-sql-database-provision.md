<properties 
	pageTitle="Provisionar um aplicativo Web que usa um banco de dados SQL" 
	description="Use um modelo do Gerenciador de Recursos do Azure para implantar um aplicativo Web que inclui um banco de dados SQL." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="tomfitz"/>

# Provisionar um aplicativo Web com um banco de dados SQL

Neste tópico, você aprenderá como criar um modelo do Gerenciador de Recursos do Azure que implanta um aplicativo Web e um banco de dados SQL. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para obter mais informações sobre a implantação de aplicativos, consulte [Implantar um aplicativo complexo de maneira previsível no Azure](app-service-deploy-complex-application-predictably.md).

Para obter o modelo completo, consulte [Modelo de aplicativo Web com banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## O que você implantará

Neste modelo, você implantará:

- um aplicativo Web
- Servidor de banco de dados SQL
- Banco de Dados SQL
- Configurações de autoescala
- Regras de alerta
- Insights de aplicativo

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/app-service-web-arm-with-sql-database-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-sql-database%2Fazuredeploy.json)

## Parâmetros para especificar

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### serverName

O nome do novo servidor de banco de dados para criar.

    "serverName": {
      "type": "string"
    }

### serverLocation

O local do servidor de banco de dados. Para melhor desempenho, esse local deve ser igual ao local do aplicativo Web.

    "serverLocation": {
      "type": "string"
    }

### administratorLogin

O nome da conta a ser usado para o administrador do servidor de banco de dados.

    "administratorLogin": {
      "type": "string"
    }

### administratorLoginPassword

A senha a ser usada para o administrador do servidor de banco de dados.

    "administratorLoginPassword": {
      "type": "securestring"
    }

### databaseName

O nome do novo banco de dados para criar.

    "databaseName": {
      "type": "string"
    }

### collation

O agrupamento de banco de dados a ser usado para controlar o uso adequado de caracteres.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

### edition

O tipo de banco de dados para criar.

    "edition": {
      "type": "string",
      "defaultValue": "Standard",
      "metadata": {
        "description": "The type of database to create. The available options are: Web, Business, Basic, Standard, and Premium."
      }
    }

### maxSizeBytes

O tamanho máximo, em bytes, do banco de dados.

    "maxSizeBytes": {
      "type": "string",
      "defaultValue": "1073741824"
    }

### requestedServiceObjectiveName

O nome correspondente ao nível de desempenho para edição.

    "requestedServiceObjectiveName": {
      "type": "string",
      "defaultValue": "S0",
      "metadata": {
        "description": "The name corresponding to the performance level for edition. The available options are: Shared, Basic, S0, S1, S2, S3, P1, P2, and P3."
      }
    }


## Recursos a implantar

### SQL Server e banco de dados

Cria um novo SQL Server e banco de dados. O nome do servidor é especificado no parâmetro **serverName** e o local é especificado no parâmetro **serverLocation**. Ao criar o novo servidor, você deve fornecer um nome de logon e senha para o administrador do servidor de banco de dados.

    {
      "name": "[parameters('serverName')]",
      "type": "Microsoft.Sql/servers",
      "location": "[parameters('serverLocation')]",
      "apiVersion": "2014-04-01-preview",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
      },
      "resources": [
        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2014-08-01",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "[parameters('edition')]",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "[parameters('maxSizeBytes')]",
            "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
          }
        },
        {
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "location": "[parameters('serverLocation')]",
          "name": "AllowAllWindowsAzureIps",
          "properties": {
            "endIpAddress": "0.0.0.0",
            "startIpAddress": "0.0.0.0"
          },
          "type": "firewallrules"
        }
      ]
    },


[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]


### Aplicativo Web

    {
      "apiVersion": "2015-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": ["[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "empty"
      },
      "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2014-11-01",
          "type": "config",
          "name": "connectionstrings",
          "dependsOn": [ "[concat('Microsoft.Web/Sites/', parameters('siteName'))]" ],
          "properties": {
              "DefaultConnection":{
              "value":"[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
              "type": 2 
            },
          }
        }
      ]
    }

### Autoescala

    {
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
      "type": "microsoft.insights/autoscalesettings",
      "location": "East US",
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"},
      "dependsOn": ["[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"],
      "properties": {
        "profiles": [
        {
          "name": "Default",
          "capacity": {
            "minimum": "1",
            "maximum": "2",
            "default": "1"
            },
            "rules": [
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 80.0
              },
              "scaleAction": 
              {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT10M"
              }
            },
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT1H",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 60.0
              },
              "scaleAction": 
              {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT1H"
              }
            }
            ]
          }
          ],
          "enabled": false,
          "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
          "targetResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        }
    }

### Regras de alerta para códigos de status 403 e 500, alta utilização da CPU e comprimento da fila de HTTP 

    {
      "apiVersion": "2014-04-01",
      "name": "[concat('ServerErrors ', parameters('siteName'))]",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": ["[concat('Microsoft.Web/sites/', parameters('siteName'))]"],
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"},
      "properties": 
      {
        "name": "[concat('ServerErrors ', parameters('siteName'))]",
        "description": "[concat(parameters('siteName'), ' has some server errors, status code 5xx.')]",
        "isEnabled": false,
        "condition": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
          "dataSource": 
          {
            "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
            "metricName": "Http5xx"
          },
          "operator": "GreaterThan",
          "threshold": 0.0,
          "windowSize": "PT5M"
        },
        "action": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": []
        }
      }
    },
    //Alert-Rules --> 403
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
            "description": "[concat(parameters('siteName'), ' has some requests that are forbidden, status code 403.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
                    "metricName": "Http403"
                },
                "operator": "GreaterThan",
                "threshold": 0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> High CPU
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
            "description": "[concat('The average CPU is high across all the instances of ', parameters('hostingPlanName'))]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "CpuPercentage"
                },
                "operator": "GreaterThan",
                "threshold": 90,
                "windowSize": "PT15M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> HTTP Queue Length
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
            "description": "[concat('The HTTP queue for the instances of ', parameters('hostingPlanName'), ' has a large number of pending requests.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "HttpQueueLength"
                },
                "operator": "GreaterThan",
                "threshold": 100.0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    }

### Insights de aplicativo

    {
        "apiVersion": "2014-04-01",
        "name": "[parameters('siteName')]",
        "type": "microsoft.insights/components",
        "location": "Central US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "ApplicationId": "[parameters('siteName')]"
        }
    }

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

### CLI do Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json


 

<!---HONumber=Oct15_HO3-->