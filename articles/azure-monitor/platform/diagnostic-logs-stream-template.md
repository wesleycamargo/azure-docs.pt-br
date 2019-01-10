---
title: Habilitar automaticamente as configurações de diagnóstico usando um modelo do Resource Manager
description: Saiba como usar um modelo do Resource Manager para criar configurações de diagnóstico que ajudarão a transmitir seus logs de diagnóstico para os Hubs de Eventos ou armazená-los em uma conta de armazenamento.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 69959db83ba99989f7c701f94cf1b17e1721bb47
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104430"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Habilitar automaticamente as Configurações de Diagnóstico na criação do recurso usando um modelo do Resource Manager
Neste artigo, mostramos como você pode usar um [Modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para definir as Configurações de Diagnóstico em um recurso quando ele é criado. Isso permite iniciar automaticamente o streaming de seus Logs de Diagnóstico e métricas para os Hubs de Eventos, arquivando-os em uma Conta de Armazenamento ou enviando-os para o Log Analytics quando um recurso é criado.

> [!WARNING]
> O formato dos dados de log na conta de armazenamento será alterado para Linhas JSON em 1º de novembro de 2018. [Confira este artigo para obter uma descrição do impacto e saber como atualizar suas ferramentas para manipular o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

O método para habilitar os Logs de Diagnóstico usando um modelo do Resource Manager depende do tipo de recurso.

* **Não Computação** (por exemplo, Grupos de Segurança de Rede, Aplicativos Lógicos, Automação) usam as [Configurações de Diagnóstico descritas neste artigo](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
* **De Computação** (baseados no WAD/LAD) usam o [Arquivo de configuração do WAD/LAD descrito neste artigo](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Neste artigo, descrevemos como configurar o diagnóstico usando um método.

Estas são as etapas básicas:

1. Crie um modelo como um arquivo JSON que descreve como criar o recurso e habilite o diagnóstico.
2. [Implantar o modelo usando qualquer método de implantação](../../azure-resource-manager/resource-group-template-deploy.md).

Abaixo, damos um exemplo de arquivo JSON do modelo que você precisa gerar para os recursos de Computação e de Não Computação.

## <a name="non-compute-resource-template"></a>Modelo de recursos de Não Computação
Para os recursos de Não Computação, você precisará fazer duas coisas:

1. Adicione parâmetros ao blob de parâmetros para o nome da conta de armazenamento, ID da regra de autorização do hub de eventos e/ou ID do workspace do Log Analytics (permitindo o arquivamento dos Logs de Diagnóstico em uma conta de armazenamento, streaming de logs para os Hubs de Eventos e/ou envio de logs para o Log Analytics).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Na matriz de recursos do recurso para o qual você deseja habilitar os Logs de Diagnóstico, adicione um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2017-05-01-preview",
        "properties": {
          "name": "[parameters('settingName')]",
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
          "eventHubName": "[parameters('eventHubName')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

O blob de propriedades da Configuração de Diagnóstico segue [o formato descrito neste artigo](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). A adição da propriedade `metrics` permitirá também o envio de métricas de recurso para essas mesmas saídas, desde que [o recurso ofereça suporte para as métricas do Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Aqui temos um exemplo completo que cria um aplicativo lógico e ativa o streaming para Hubs de eventos e o armazenamento em uma conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modelo de recursos de computação
Para habilitar o diagnóstico em um recurso de Computação, por exemplo uma Máquina Virtual ou cluster do Service Fabric, você precisa:

1. Adicionar a extensão de Diagnóstico do Azure à definição do recurso da VM.
2. Especificar uma conta de armazenamento e/ou hub de eventos como um parâmetro.
3. Adicionar o conteúdo do arquivo XML WADCfg na propriedade XMLCfg, substituindo todos os caracteres XML corretamente.

> [!WARNING]
> Esta última etapa pode ser difícil de fazer. [Confira este artigo](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o Esquema de Configuração de Diagnóstico em variáveis que são substituídas e formatadas corretamente.
> 
> 

O processo inteiro, incluindo os exemplos, é descrito [neste documento](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os Logs de Diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)

