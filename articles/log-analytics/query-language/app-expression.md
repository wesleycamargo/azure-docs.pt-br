---
title: Expressão app() na consulta do Azure Log Analytics | Microsoft Docs
description: A expressão app é usada em uma consulta do Log Analytics para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura.
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
ms.openlocfilehash: d91e148320c4c59bb888975499aa1de16ffbf134
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955054"
---
# <a name="app-expression-in-log-analytics-query"></a>Expressão app() na consulta do Log Analytics

A expressão `app` é usada em uma consulta do Log Analytics para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura. Isso é útil para incluir dados de aplicativo em uma consulta do Log Analytics e para consultar dados em vários aplicativos em uma consulta do Application Insights.



## <a name="syntax"></a>Sintaxe

`app(`*Identificador*`)`


## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o aplicativo usando um dos formatos na tabela a seguir.

| Identificador | DESCRIÇÃO | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível do aplicativo (também conhecido como "nome do componente") | app("fabrikamapp") |
| Nome Qualificado | Nome completo do aplicativo no formato: "subscriptionName/resourceGroup/componentName" | app('AI-prototype/Fabrikam/fabrikamapp') |
| ID | GUID do aplicativo | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID do recurso do Azure | Identificador do recurso do Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Observações

* Você deve ter acesso de leitura ao aplicativo.
* A identificação de um aplicativo por seu nome presume que ele seja exclusivo em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.
* Use a expressão relacionada [workspace](workspace-expression.md) para consultar em workspaces do Log Analytics.

## <a name="examples"></a>Exemplos

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Veja a [expressão do workspace](workspace-expression.md) para se referir ao workspace do Log Analytics.
- Leia sobre como os [Dados do Log Analytics](../../log-analytics/log-analytics-log-search.md) são armazenados.