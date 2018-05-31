---
title: Exibir logs de atividade para alterações RBAC no Azure | Microsoft Docs
description: Exibir os logs de atividade para alterações de controle de acesso baseado em função para os últimos 90 dias.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e09ccdc4942a39e54b760cb5ad78c035dbc05f8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312416"
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>Exibir os logs de atividade para alterações de controle de acesso baseado em função

Sempre que alguém faz alterações em definições de função ou atribuições de função dentro de suas assinaturas, as alterações são registradas [o Log de atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) na categoria Administrativa. Você pode exibir os logs de atividade para ver todas as alterações RBAC (controle) de acesso baseado em função para os últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registradas em log

Aqui estão as operações relacionadas à RBAC que são registradas no Activity Log:

- Criar ou atualizar definição de função personalizada
- Excluir definição de função personalizada
- Criar atribuição de função
- Excluir atribuição de função

## <a name="azure-portal"></a>Portal do Azure

A maneira mais fácil para começar é exibir os logs de atividade com o portal do Azure. A captura de tela a seguir mostra um exemplo de um log de atividade que foi filtrado para exibir a categoria **administrativo** junto com a definição de função e as operações de atribuição de função. Ele também inclui um link para baixar os logs como um arquivo CSV.

![Logs de atividade usando o portal – captura de tela](./media/change-history-report/activity-log-portal.png)

Para obter mais informações, consulte [Visualizar eventos no log de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Para exibir logs de atividade com o Azure PowerShell, use o comando [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog).

Esse comando abaixo lista todas as alterações de atribuição de função em uma assinatura nos os últimos sete dias:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Esse comando abaixo lista todas as definição de atribuição de função em um grupo de recursos nos os últimos sete dias:

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Esse comando lista todas as alterações de atribuição de função e de definição de função em uma assinatura para os últimos sete dias e exibe os resultados em uma lista:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>CLI do Azure

Para exibir logs de atividade com a CLI do Azure, use o comando [lista de log de atividades do monitor az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Esse comando lista os logs de atividade em um grupo de recursos desde a hora de início:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Esse comando lista os logs de atividade para um provedor de recursos de autorização desde a hora de início:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure Log Analytics](../log-analytics/log-analytics-overview.md) é outra ferramenta que você pode usar para coletar e analisar as alterações de controle de acesso baseado em função para todos os recursos do Azure. o Log Analytics oferece as seguintes vantagens:

- Escrever consultas complexas e lógica
- Integrar com alertas, Power BI e outras ferramentas
- Salvar dados por períodos de retenção mais longos
- Referência cruzada com outros logs, como segurança, máquina virtual e personalizados

Aqui estão as etapas básicas para começar:

1. [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. [Configurar a solução Log Analytics da atividade](../log-analytics/log-analytics-activity.md#configuration) para seu espaço de trabalho.

1. [Exibir os logs de atividade](../log-analytics/log-analytics-activity.md#using-the-solution). Uma maneira rápida de navegar até a página de visão geral do Log Analytics da atividade é clicar na opção **Log Analytics**.

   ![Opção Log Analytics no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Opcionalmente, use a página [pesquisa de Log](../log-analytics/log-analytics-log-search.md) ou o [portal Advanced Analytics](https://docs.loganalytics.io/docs/Learn) para consultar e exibir os logs. Para obter mais informações sobre essas duas opções, consulte [Página de pesquisa de Log ou o portal Advanced Analytics](../log-analytics/log-analytics-log-search-portals.md).

Aqui está uma consulta que retorna novas atribuições de função organizadas por provedor de recursos de destino:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Aqui está uma consulta que retorna as alterações de atribuição de função exibidas em um gráfico:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Logs de atividade usando o portal Advanced Analytics - captura de tela](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Próximas etapas
* [Exibir eventos no log de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorar a atividade da assinatura com o Log de Atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
