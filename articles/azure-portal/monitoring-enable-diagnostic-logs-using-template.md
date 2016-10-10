<properties
	pageTitle="Habilitar automaticamente as Configurações de Diagnóstico usando um modelo do Resource Manager | Microsoft Azure"
	description="Saiba como usar um modelo do Resource Manager para criar configurações de diagnóstico que ajudarão a transmitir seus logs de diagnóstico para os Hubs de Eventos ou armazená-los em uma conta de armazenamento."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="johnkem"/>

# Habilitar automaticamente as Configurações de Diagnóstico na criação do recurso usando um modelo do Resource Manager
Neste artigo, mostramos como você pode usar um [Modelo do Azure Resource Manager](../resource-group-authoring-templates.md) para definir as Configurações de Diagnóstico em um recurso quando ele é criado. Isso permite iniciar automaticamente o streaming de seus Logs de Diagnóstico e métricas para os Hubs de Eventos, arquivando-os em uma Conta de Armazenamento ou enviando-os para o Log Analytics quando um recurso é criado.

O método para habilitar os Logs de Diagnóstico usando um modelo do Resource Manager depende do tipo de recurso.

- Os recursos de **Não Computação** (por exemplo, Grupos de Segurança de Rede, Aplicativos Lógicos, Automação) usam as [Configurações de Diagnóstico descritas neste artigo](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- Os recursos **De Computação** (baseados no WAD/LAD) usam o [Arquivo de configuração do WAD/LAD descrito neste artigo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Neste artigo, descrevemos como configurar o diagnóstico usando um método.

Estas são as etapas básicas:

1. Crie um modelo como um arquivo JSON que descreve como criar o recurso e habilite o diagnóstico.
2. [Implantar o modelo usando qualquer método de implantação](../resource-group-template-deploy.md).

Abaixo, damos um exemplo de arquivo JSON do modelo que você precisa gerar para os recursos de Computação e de Não Computação.

## Modelo de recursos de Não Computação
Para os recursos de Não Computação, você precisará fazer duas coisas:

1. Adicione parâmetros ao blob de parâmetros para o nome da conta de armazenamento, ID de regra do barramento de serviço e/ou ID do espaço de trabalho do Log Analytics do OMS (permitindo o arquivamento dos Logs de Diagnóstico em uma conta de armazenamento, streaming de logs para os Hubs de Eventos e/ou envio de logs para o Log Analytics).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Na matriz de recursos do recurso para o qual você deseja habilitar os Logs de Diagnóstico, adicione um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
          ]
        }
      }
    ]
    ```

O blob de propriedades da Configuração de Diagnóstico segue [o formato descrito neste artigo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Aqui está um exemplo completo que cria um Grupo de Segurança da Rede e ativa o streaming para os Hubs de Eventos e o armazenamento em uma conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
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
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
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

## Modelo de recursos de computação
Para habilitar o diagnóstico em um recurso de Computação, por exemplo uma Máquina Virtual ou cluster do Service Fabric, você precisa:

1. Adicionar a extensão de Diagnóstico do Azure à definição do recurso da VM.
2. Especificar uma conta de armazenamento e/ou hub de eventos como um parâmetro.
3. Adicionar o conteúdo do arquivo XML WADCfg na propriedade XMLCfg, substituindo todos os caracteres XML corretamente.

> [AZURE.WARNING] Esta última etapa pode ser difícil de fazer. [Confira este artigo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o Esquema de Configuração de Diagnóstico em variáveis que são substituídas e formatadas corretamente.

O processo inteiro, incluindo os exemplos, é descrito [neste documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## Próximas etapas
- [Saiba mais sobre os Logs de Diagnóstico do Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos](./monitoring-stream-diagnostic-logs-to-event-hubs.md)

<!---HONumber=AcomDC_0928_2016-->