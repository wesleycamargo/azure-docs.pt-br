---
title: Coletar métricas e logs de serviço do Azure no espaço de trabalho do Log Analytics | Microsoft Docs
description: Configure o diagnóstico nos recursos do Azure para gravar logs e métricas ao espaço de trabalho do Log Analytics no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006270"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Coletar métricas e logs de serviço do Azure no espaço de trabalho do Log Analytics no Azure Monitor

Há quatro maneiras diferentes de coletar logs e métricas para os serviços do Azure:

1. Direcionar o diagnóstico do Azure para o espaço de trabalho do Log Analytics no Azure Monitor (*diagnóstico* na tabela a seguir)
2. O diagnóstico do Azure no armazenamento do Azure ao espaço de trabalho do Log Analytics no Azure Monitor (*armazenamento* na tabela a seguir)
3. Conectores para serviços do Azure (*Conectores* na tabela a seguir)
4. Scripts para coletar e postar dados para o espaço de trabalho do Log Analytics no Azure Monitor (espaços em branco na tabela a seguir e para os serviços que não estão listados)


| Serviço                 | Tipo de recurso                           | Logs        | Métricas     | Solução |
| --- | --- | --- | --- | --- |
| Application gateways    | Microsoft.Network/applicationGateways   | Diagnósticos | Diagnósticos | [Análise de Gateway de Aplicativo do Azure](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application insights    |                                         | Conector   | Conector   | [Conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (visualização) |
| Contas de automação     | Microsoft.Automation/AutomationAccounts | Diagnósticos |             | [Mais informações](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Contas do Lote          | Microsoft.Batch/batchAccounts           | Diagnósticos | Diagnósticos | |
| Serviços de Nuvem clássicos  |                                         | Armazenamento     |             | [Mais informações](azure-storage-iis-table.md) |
| Serviços cognitivos      | Microsoft.CognitiveServices/accounts    |             | Diagnósticos | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnósticos |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnósticos |             | |
| Namespace do Hub de Eventos     | Microsoft.EventHub/namespaces           | Diagnósticos | Diagnósticos | |
| Hubs IoT                | Microsoft.Devices/IotHubs               |             | Diagnósticos | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnósticos |             | [KeyVault Analytics](../insights/azure-key-vault.md) |
| Balanceadores de Carga          | Microsoft.Network/loadBalancers         | Diagnósticos |             |  |
| Aplicativos Lógicos              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnósticos | Diagnósticos | |
| Grupos de segurança de rede | Microsoft.Network/networksecuritygroups | Diagnósticos |             | [Análise de Grupo de Segurança de Rede do Azure](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Cofres de recuperação         | Microsoft.RecoveryServices/vaults       |             |             | [Serviços de recuperação do Azure Analytics (visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Serviços Search         | Microsoft.Search/searchServices         | Diagnósticos | Diagnósticos | |
| Namespace do Barramento de Serviço   | Microsoft.ServiceBus/namespaces         | Diagnósticos | Diagnósticos | [Análise de barramento de serviço (visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Armazenamento     |             | [Análise do Service Fabric (versão prévia)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnósticos | [Azure SQL Analytics (Visualização)](../insights/azure-sql.md) |
| Armazenamento                 |                                         |             | Script      | [Análise de armazenamento do Azure (visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Máquinas Virtuais        | Microsoft.Compute/virtualMachines       | Extensão   | Extensão <br> Diagnósticos  | |
| Conjuntos de dimensionamento de Máquinas Virtuais | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnósticos | |
| Farms do servidor Web        | Microsoft.Web/serverfarms               |             | Diagnósticos | |
| Sites               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnósticos | [Análise de Aplicativos Web do Azure (Visualização)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Para o monitoramento de máquinas virtuais do Azure (Linux e Windows), é recomendável instalar a [extensão de VM do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md). O agente fornece informações coletadas das suas máquinas virtuais. Você também pode usar a extensão para ps conjuntos de dimensionamento de máquinas virtuais.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Direcionar o diagnóstico do Azure para o Log Analytics
Muitos recursos do Azure são capazes de gravar logs de diagnóstico e métricas diretamente para um espaço de trabalho do Log Analytics no Azure Monitor, e essa é a maneira preferida de coleta de dados para análise. Ao usar o diagnóstico do Azure, os dados são gravados imediatamente no espaço de trabalho, e não é necessário primeiro gravar os dados para o armazenamento.

Recursos do Azure que dão suporte ao [o Azure monitor](../../azure-monitor/overview.md) pode enviar os logs e métricas diretamente para um espaço de trabalho do Log Analytics.

> [!NOTE]
> Envio de métricas multidimensionais para um espaço de trabalho do Log Analytics por meio de configurações de diagnóstico não é suportado atualmente. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: A métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica é representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
>

* Para obter os detalhes das métricas disponíveis, consulte [métricas compatíveis com o Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Para obter os detalhes dos logs disponíveis, consulte [serviços e esquema com suporte para logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Habilitar diagnóstico com PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo de PowerShell a seguir mostra como usar [AzDiagnosticSetting conjunto](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para habilitar o diagnóstico em um grupo de segurança de rede. A mesma abordagem funciona para todos os recursos com suporte: defina `$resourceId` para a ID do recurso para a qual você quer habilitar os diagnósticos.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Habilitar diagnósticos com modelos do Resource Manager

Você pode usar um modelo semelhante ao mostrado abaixo para habilitar diagnósticos em um recurso quando ele é criado e fazer com que ele seja enviado para seu espaço de trabalho do Log Analytics. Este exemplo destina-se a uma conta de automação, mas funciona em todos os tipos de recursos com suporte.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Enviar o diagnóstico do Azure para o armazenamento e depois para o Log Analytics

Para coletar logs de dentro de alguns recursos, é possível enviar os logs no armazenamento do Azure e, em seguida, configure o espaço de trabalho do Log Analytics para ler os logs do armazenamento.

O Azure Monitor pode usar essa abordagem para coletar o diagnóstico do armazenamento do Azure para os seguintes recursos e os logs:

| Recurso | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Evento operacional <br> Evento de ator confiável <br> Evento de serviço confiável |
| Máquinas Virtuais |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |
| Funções web <br> Funções de trabalho |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |

> [!NOTE]
> Você é cobrado a taxas de dados do Azure normais para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento e quando o espaço de trabalho do Log Analytics ler os dados de sua conta de armazenamento.
>
>

Ver [usar armazenamento de BLOBs para IIS e armazenamento de tabelas para eventos](azure-storage-iis-table.md) para saber mais sobre como o Azure Monitor pode coletar esses logs.

## <a name="connectors-for-azure-services"></a>Conectores para serviços do Azure

Há um conector para o Application Insights, que permite que os dados coletados pelo Application Insights sejam enviados para um espaço de trabalho do Log Analytics.

Saiba mais sobre o [conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>Scripts para coletar e postar dados no espaço de trabalho do Log Analytics

Para serviços do Azure que não fornecem uma maneira direta de enviar logs e métricas para um espaço de trabalho do Log Analytics, você pode usar um script de automação do Azure para coletar os logs e métricas. O script pode enviar os dados para o espaço de trabalho usando o [API do coletor de dados](../../azure-monitor/platform/data-collector-api.md)

A Galeria de modelos do Azure tem [exemplos de como usar a automação do Azure](https://azure.microsoft.com/resources/templates/?term=OMS) coletar dados de serviços e enviá-lo para o Azure Monitor.

## <a name="next-steps"></a>Próximas etapas

* [Use o armazenamento de blobs para IIS e armazenamento de tabelas para eventos](azure-storage-iis-table.md) para ler os logs de serviços do Azure que gravam diagnósticos em armazenamento de tabelas ou de logs do IIS gravados para armazenamento de blobs.
* [Habilitar Soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.
