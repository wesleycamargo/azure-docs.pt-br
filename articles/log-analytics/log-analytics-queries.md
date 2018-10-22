---
title: Analisar dados do Log Analytics no Azure Monitor | Microsoft Docs
description: Você precisará de uma pesquisa de logs para recuperar quaisquer dados do Log Analytics.  Este artigo descreve como novas pesquisas de logs são utilizadas no Log Analytics e fornece conceitos necessários para serem compreendidos antes de criar uma.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: d7c006ca0be5e8db4b7ab02974ff029d3fe738e3
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042318"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Analisar dados do Log Analytics no Azure Monitor

Os dados de log coletados pelo Azure Monitor são armazenados no Log Analytics que coleta a telemetria e outros dados de uma variedade de fontes e fornece uma linguagem de consulta para análise avançada.

> [!NOTE]
> Anteriormente, o Log Analytics era tratado como seu próprio serviço no Azure. Ele agora é considerado parte do Azure Monitor e concentra-se em armazenamento e análise de dados de log usando sua linguagem de consulta. Os recursos que eram considerados parte do Log Analytics, como agentes do Windows e do Linux para coleta de dados, modos de exibição para visualizar os dados existentes e alertas para notificar proativamente sobre problemas, não foram alterados, mas agora são considerados parte do Azure Monitor.



## <a name="log-queries"></a>Consultas de logs

Você precisa de uma pesquisa de logs para recuperar dados do Log Analytics.  Se estiver [analisando dados no portal](log-analytics-log-search-portals.md), [configurando uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para ser notificado sobre uma condição específica ou recuperando dados usando a [API do Log Analytics](https://dev.loganalytics.io/), você usará uma consulta para especificar os dados desejados.  Este artigo descreve como as consultas de logs são usadas no Log Analytics e fornece conceitos que deverão ser compreendidos antes que elas sejam criadas.



## <a name="where-log-queries-are-used"></a>Onde as consultas de logs são usadas

As diferentes maneiras de usar consultas no Log Analytics incluem:

- **Portais.** Você pode executar análises interativas de dados de log no [portal do Azure](log-analytics-log-search-portals.md).  Isso permite que você edite sua consulta e analise os resultados em uma variedade de formatos e visualizações.  
- **Regras de alerta** As [Regras de alerta](log-analytics-alerts.md) identificam proativamente os problemas dos dados no espaço de trabalho.  Cada regra de alerta é baseada em uma pesquisa de logs que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Painéis.** Você pode fixar os resultados da consulta em um [painel do Azure](), que permite visualizar os dados de log e de métrica em conjunto e, opcionalmente, compartilhar com outros usuários do Azure. 
- **Exibições.**  Você pode criar visualizações de dados a serem incluídas em painéis de usuários com [Designer de Exibição](log-analytics-view-designer.md).  As consultas de logs fornecem os dados usados por [blocos](log-analytics-view-designer-tiles.md) e [blocos de visualização](log-analytics-view-designer-parts.md) em cada exibição.  
- **Exportação.**  Ao importar dados do workspace do Log Analytics para o Excel ou o [Power BI](log-analytics-powerbi.md), você cria uma consulta de logs para definir os dados a serem exportados.
- **PowerShell.** É possível executar um script do PowerShell a partir de uma linha de comando ou um runbook de Automação do Azure que utiliza [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) para recuperar dados do Log Analytics.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API do Log Analytics.**  A [API da pesquisa de logs do Log Analytics](log-analytics-log-search-api.md) permite que qualquer cliente da API REST recupere dados de log do workspace.  A solicitação de API inclui uma consulta que é executada no Log Analytics para determinar os dados a serem recuperados.

![Pesquisas de log](media/log-analytics-queries/queries-overview.png)

## <a name="write-a-query"></a>Escreva uma consulta
O Log Analytics inclui [uma ampla linguagem de consulta](query-language/get-started-queries.md) que permite recuperar e analisar dados de log em uma variedade de formas.  Normalmente, você começa com consultas básicas e, depois, progride para utilizar funções mais avançadas na medida em que seus requisitos se tornam mais complexos.

A estrutura básica de uma consulta é uma tabela de origem seguida de uma série de operadores separados por um caractere de pipe `|`.  É possível encadear várias operadores para refinar os dados e executar funções avançadas.

Por exemplo, suponha que você deseja localizar os dez melhores computadores com mais eventos de erro no último dia.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Ou talvez você queira localizar computadores que não tiveram pulsação no último dia.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

E quanto a um gráfico de linhas com a utilização do processador para cada computador da semana passada?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

É possível consultar essas amostras rápidas que, independentemente do tipo de dados com o qual você está trabalhando, a estrutura da consulta é similar.  Você pode dividir em etapas distintas onde os dados resultantes de um comando são enviados através do pipeline para o próximo comando.

Você também pode consultar dados em workspaces do Log Analytics dentro de sua assinatura.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 

## <a name="how-log-analytics-data-is-organized"></a>Como os dados do Log Analytics são organizados
Ao criar uma consulta, você começa determinando quais tabelas possuem os dados procurados. Diferentes tipos de dados são separados em tabelas dedicadas em cada [workspace do Log Analytics](log-analytics-quick-create-workspace.md).  A documentação das diferentes fontes de dados inclui o nome do tipo de dados que ela cria e uma descrição de cada uma de suas propriedades.  Muitas consultas somente requerem dados de uma única tabela, mas outras podem utilizar uma variedade de opções para incluir dados de várias tabelas.

Embora o [Application Insights](../application-insights/app-insights-overview.md) armazene dados de aplicativo, como solicitações, exceções, rastreamentos e uso no Log Analytics, esses dados são armazenados em uma partição diferente dos outros dados de log. A mesma linguagem de consulta é usada para acessar esses dados, mas é necessário usar o [console do Application Insights](../application-insights/app-insights-analytics.md) ou a [API REST do Application Insights](https://dev.applicationinsights.io/) para acessá-los. Você pode usar [consultas entre recursos](log-analytics-cross-workspace-search.md) para combinar dados do Application Insights com outros dados no Log Analytics.


![Tabelas](media/log-analytics-queries/queries-tables.png)







## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [portais utilizados para criar e editar pesquisas de logs](log-analytics-log-search-portals.md).
- Confira um [tutorial sobre como escrever consultas](log-analytics-tutorial-viewdata.md) utilizando a nova linguagem de consulta.
