---
title: Expressão workspace() na consulta do Azure Log Analytics | Microsoft Docs
description: A expressão workspace é usada em uma consulta do Log Analytics para recuperar dados de workspace específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 8b83650acfee638ae2a667cd0925fd91d44b80b2
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282689"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Expressão workspace() na consulta do Log Analytics

A expressão `workspace` é usada em uma consulta do Log Analytics para recuperar dados de workspace específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura. Isso é útil para incluir dados de log em uma consulta do Application Insights e para consultar dados em vários workspaces em uma consulta de log.


## <a name="syntax"></a>Sintaxe

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o workspace usando um dos formatos na tabela a seguir.

| Identificador | DESCRIÇÃO | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível do workspace (também conhecido como "nome do componente") | workspace("contosoretail") |
| Nome Qualificado | Nome completo do workspace no formato: "subscriptionName/resourceGroup/componentName" | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID do workspace | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID do recurso do Azure | Identificador do recurso do Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Observações

* Você deve ter acesso de leitura ao workspace.
* Uma expressão relacionada é `app`, que permite consultar entre aplicativos do Application Insights.

## <a name="examples"></a>Exemplos

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Próximas etapas

- Confira a [expressão aplicativo](workspace-expression.md) para se referir a aplicativo do Application Insights.
- Leia sobre como os [Dados do Log Analytics](../../log-analytics/log-analytics-queries.md) são armazenados.