---
title: Usar modelos do Azure Resource Manager para criar e configurar um workspace do Log Analytics | Microsoft Docs
description: Você pode usar os modelos do Azure Resource Manager para criar e configurar workspaces do Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: magoedte
ms.openlocfilehash: c99fb3362af888d576f14940113bb49ccb7a1f08
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191301"
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Gerenciar Log Analytics usando modelos do Azure Resource Manager
Você pode usar os [modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para criar e configurar workspaces do Log Analytics. Os exemplos das tarefas que você pode executar com os modelos incluem:

* Criar um workspace incluindo o tipo de preço de configuração 
* Adicionar uma solução
* Criar pesquisas salvas
* Criar um grupo de computadores
* Habilitar coleta de logs do IIS de computadores com o agente do Windows instalado
* Coletar contadores de desempenho de computadores Linux e Windows
* Coletar eventos de syslog em computadores Linux 
* Coletar eventos dos logs de eventos do Windows
* Adicionar o agente de análise de log a uma máquina virtual do Azure
* Configurar a análise de log para indexar os dados coletados usando o diagnóstico do Azure

Este artigo fornece exemplos de código que ilustram algumas das configurações que podem ser executadas com os modelos.

## <a name="api-versions"></a>Versões de API
A tabela a seguir lista a versão de API para os recursos usados neste exemplo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|:---|
| Workspace   | workspaces    | 2017-03-15-preview |
| Search      | savedSearches | 2017-03-15-preview |
| Fonte de dados | datasources   | 2015-11-01-preview |
| Solução    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Criar um workspace do Log Analytics
O exemplo a seguir cria um workspace usando um modelo da sua máquina local. O modelo JSON está configurado para solicitar apenas o nome do workspace e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente.  

Os parâmetros a seguir definem um valor padrão:

* Local - o padrão é Leste dos EUA
* SKU - padrão para o novo tipo de preços por GB lançado no modelo de preço de abril de 2018

>[!WARNING]
>Se criar ou configurar um workspace do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Criar e implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. Edite o modelo para atender às suas necessidades.  Revisão de referência[Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) para saber quais propriedades e os valores são suportados. 
3. Salve esse arquivo como **deploylaworkspacetemplate.json** para uma pasta local.
4. Você está pronto para implantar o modelo. Use o PowerShell ou a linha de comando para criar o workspace.

   * No caso do PowerShell, use os seguintes comandos na pasta que contém o modelo:
   
        ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * No caso da linha de comando, use os seguintes comandos na pasta que contém o modelo:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:<br><br> ![Resultados de exemplo, quando a implantação for concluída](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Criar um workspace do Log Analytics
O exemplo de modelo a seguir ilustra como:

1. Adicionar soluções ao workspace
2. Criar pesquisas salvas
3. Criar um grupo de computadores
4. Habilitar coleta de logs do IIS de computadores com o agente do Windows instalado
5. Coletar contadores de desempenho de disco lógico de computadores Linux (% Used Inodes; Free Megabytes; % Used Space; Disk Transfers/sec; Disk Reads/sec; Disk Writes/sec)
6. Coletar eventos de syslog em computadores Linux
7. Coletar eventos de Erro e Aviso no Log de Eventos do Aplicativo dos computadores Windows
8. Coletar o contador de desempenho de Mbytes Disponíveis de Memória de computadores Windows
9. Coletar logs do IIS e logs de Eventos do Windows gravados pelo diagnóstico do Azure para uma conta de armazenamento

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and Log Analytics plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2017-03-15-preview",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Implantar o modelo de exemplo
Para implantar o modelo de exemplo:

1. Salve o exemplo anexado em um arquivo, por exemplo `azuredeploy.json` 
2. Edite o modelo para obter a configuração desejada
3. Use o PowerShell ou a linha de comando para implantar o modelo

#### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Linha de comando
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Exemplo de modelos do Resource Manager
A Galeria de modelos de início rápido do Azure inclui vários modelos para Log Analytics, incluindo:

* [Implantar uma máquina virtual executando o Windows com a extensão de VM do Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Implantar uma máquina virtual executando o Linux com a extensão de VM do Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorar o Azure Site Recovery usando um workspace do Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorar o Azure Web Apps usando um workspace do Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Adicionar uma conta de armazenamento existente ao Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Próximas etapas
* [Implantar agente do Windows a VMs do Azure usando modelos do Gerenciador de Recursos](../../virtual-machines/extensions/oms-windows.md).
* [Implantar agente do Linux a VMs do Azure usando modelos do Gerenciador de Recursos](../../virtual-machines/extensions/oms-linux.md).

