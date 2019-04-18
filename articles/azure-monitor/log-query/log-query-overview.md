---
title: Analisar dados de log no Azure Monitor | Microsoft Docs
description: Você precisa de uma consulta de log para recuperar dados de log do Azure Monitor.  Este artigo descreve como novas consultas de log são utilizadas no Azure Monitor e fornece conceitos necessários para serem compreendidos antes de criar uma.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: dcac701f3c1b6d64a7017c31679c019b91103ba2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904722"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analisar dados de log no Azure Monitor

Os dados de log coletados pelo Monitor do Azure são armazenados em um espaço de trabalho do Log Analytics, que é baseado no [Azure Data Explorer](/azure/data-explorer). Ele coleta a telemetria de várias origens e usa a [linguagem de consulta Kusto](/azure/kusto/query) usada pelo Data Explorer para recuperar e analisar dados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>Consultas de logs

Você precisa de uma consulta de log para recuperar quaisquer dados de log do Azure Monitor.  Se estiver [analisando dados no portal](portals.md), [configurando uma regra de alerta](../platform/alerts-metric.md) para ser notificado sobre uma condição específica ou recuperando dados usando a [API de Logs do Azure Monitor](https://dev.loganalytics.io/), você usará uma consulta para especificar os dados desejados.  Este artigo descreve como as consultas de logs são usadas no Azure Monitor e fornece conceitos que deverão ser compreendidos antes que elas sejam criadas.

## <a name="where-log-queries-are-used"></a>Onde as consultas de logs são usadas

As diferentes maneiras que você usará consultas no Azure Monitor incluem o seguinte:

- **Portal.** Você pode executar análises interativas de dados de log no [portal do Azure](portals.md).  Isso permite que você edite sua consulta e analise os resultados em uma variedade de formatos e visualizações.  
- **Regras de alerta** As [Regras de alerta](../platform/alerts-overview.md) identificam proativamente os problemas dos dados no workspace.  Cada regra de alerta é baseada em uma pesquisa de logs que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Painéis.** Você pode fixar os resultados da consulta em um [painel do Azure](../learn/tutorial-logs-dashboards.md), que permite visualizar os dados de log e de métrica em conjunto e, opcionalmente, compartilhar com outros usuários do Azure. 
- **Exibições.**  Você pode criar visualizações de dados a serem incluídas em painéis de usuários com [Designer de Exibição](../platform/view-designer.md).  As consultas de logs fornecem os dados usados por [blocos](../platform/view-designer-tiles.md) e [blocos de visualização](../platform/view-designer-parts.md) em cada exibição.  

- **Exportação.**  Ao importar dados de log do Azure Monitor para o Excel ou o [Power BI](../platform/powerbi.md), você cria uma consulta de logs para definir os dados a serem exportados.
- **PowerShell.** Você pode executar um script do PowerShell de uma linha de comando ou um runbook de automação do Azure que usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresults) para recuperar dados de log do Azure Monitor.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API de Logs do Azure Monitor.**  A [API de Logs do Azure Monitor](../platform/alerts-overview.md) permite que qualquer cliente da API REST recupere dados de log do workspace.  A solicitação de API inclui uma consulta que é executada no Azure Monitor para determinar os dados a serem recuperados.

![Pesquisas de log](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Escreva uma consulta
O Azure Monitor usa [uma versão da linguagem de consulta Kusto](get-started-queries.md) para recuperar e analisar dados de registro de várias maneiras.  Normalmente, você começa com consultas básicas e, depois, progride para utilizar funções mais avançadas na medida em que seus requisitos se tornam mais complexos.

A estrutura básica de uma consulta é uma tabela de origem seguida de uma série de operadores separados por um caractere de pipe `|`.  É possível encadear várias operadores para refinar os dados e executar funções avançadas.

Por exemplo, suponha que você deseja localizar os dez melhores computadores com mais eventos de erro no último dia.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Ou talvez você queira localizar computadores que não tiveram pulsação no último dia.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

E quanto a um gráfico de linhas com a utilização do processador para cada computador da semana passada?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

É possível consultar essas amostras rápidas que, independentemente do tipo de dados com o qual você está trabalhando, a estrutura da consulta é similar.  Você pode dividir em etapas distintas onde os dados resultantes de um comando são enviados através do pipeline para o próximo comando.

Você também pode consultar dados em workspaces do Log Analytics dentro de sua assinatura.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Como os dados de log do Azure Monitor são organizados
Ao criar uma consulta, você começa determinando quais tabelas possuem os dados procurados. Diferentes tipos de dados são separados em tabelas dedicadas em cada [espaço de trabalho do Log Analytics](../learn/quick-create-workspace.md).  A documentação das diferentes fontes de dados inclui o nome do tipo de dados que ela cria e uma descrição de cada uma de suas propriedades.  Muitas consultas somente requerem dados de uma única tabela, mas outras pessoas podem usar uma variedade de opções para incluir dados de várias tabelas.

Embora o [Application Insights](../app/app-insights-overview.md) armazene dados de aplicativo, como solicitações, exceções, rastreamentos e uso no Azure Monitor, esses dados são armazenados em uma partição diferente dos outros dados de log. Você usar a mesma linguagem de consulta para acessar esses dados, mas deve usar o [console do Application Insights](../app/analytics.md) ou [API REST do Application Insights](https://dev.applicationinsights.io/) para acessá-lo. Você pode usar [consultas entre recursos](../log-query/cross-workspace-query.md) para combinar dados do Application Insights com outros dados de log no Azure Monitor.


![Tabelas](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar [pesquisas de log do Log Analytics para criar e editar](../log-query/portals.md).
- Confira um [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) utilizando a nova linguagem de consulta.
