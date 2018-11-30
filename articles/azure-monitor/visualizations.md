---
title: Visualizando dados do Azure Monitor | Microsoft Docs
description: Fornece um resumo dos métodos disponíveis para visualizar dados armazenados no Monitor do Azure, incluindo dados do repositório de métricas e do Log Analytics.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: 4200accee94840a8a4455308f24a857fd5d7034a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632969"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizando dados do Monitor do Azure
Este artigo fornece um resumo dos métodos disponíveis para visualizar dados armazenados no Monitor do Azure. Isso inclui [métricas no armazenamento de métricas do Azure](../azure-monitor/platform/data-collection.md#metrics) e [dados de log no Log Analytics](../azure-monitor/platform/data-collection.md#logs). 

Visualizações como tabelas e gráficos podem ajudá-lo a analisar seus dados de monitoramento para detalhar problemas e identificar padrões. Dependendo da ferramenta que você usa, você também pode ter a opção de compartilhar visualizações com outros usuários dentro e fora de sua organização.

## <a name="azure-dashboards"></a>Painéis do Azure
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de painéis do Azure. Eles são particularmente úteis para fornecer um único painel de controle sobre sua infraestrutura e serviços do Azure, permitindo que você identifique rapidamente problemas importantes.

![painel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Vantagens
- Integração profunda no Azure. As visualizações podem ser fixadas em painéis de várias páginas do Azure, incluindo o Metrics Explorer, o Log Analytics e o Application Insights.
- Suporta métricas e logs.
- Combine dados de várias origens, incluindo a saída do [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md), [Log Analytics consultas](../log-analytics/log-analytics-queries.md) e [mapas](../application-insights/app-insights-app-map.md) e [disponibilidade]() no Application Insights.
- Opção para os painéis de pessoais ou compartilhadas. Integrado com a autenticação baseada em função do Azure [(RBAC)](../role-based-access-control/overview.md).
- Atualização automática. A atualização de métricas depende do intervalo de tempo com um mínimo de cinco minutos. Registra a atualização em um minuto.
- Painéis de métricas parametrizadas com carimbo de data/hora e parâmetros personalizados.
- Opções de layout flexível.
- Modo de tela inteira.


### <a name="limitations"></a>Limitações
- Controle limitado sobre as visualizações do Log Analytics sem suporte para tabelas de dados. O número total de séries de dados é limitado a 10 com outras séries de dados agrupadas em um _outro_ intervalo.
- Não há suporte para parâmetros personalizados para gráficos do Log Analytics.
- Os gráficos do Log Analytics estão limitados aos últimos 30 dias.
- Os gráficos do Log Analytics só podem ser fixados em painéis compartilhados.
- Nenhuma interatividade com dados do painel.
- Detalhamento contextual limitado.

## <a name="azure-monitor-views"></a>Modos de exibição do Monitor do Azure
[Exibições no Monitor do Azure](../azure-monitor/platform/view-designer.md) permitem que você crie visualizações personalizadas com dados de log armazenados no Log Analytics. Elas são usadas pelas [soluções de monitoramento](../azure-monitor/insights/solutions.md) para apresentar os dados coletados.

![Visualizar](media/visualizations/view.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas para dados do Log Analytics.
- Exporte e importe visualizações para transferi-las para outros grupos de recursos e assinaturas.
- Integra-se ao modelo de gerenciamento do Log Analytic com workspaces e soluções de monitoramento.
- [Filtros](../azure-monitor/platform/view-designer-filters.md) para parâmetros personalizados.
- Interativo, suporta drill-in de vários níveis (veja as brocas em outra visão)

### <a name="limitations"></a>Limitações
- Suporta logs, mas não métricas.
- Não há exibições pessoais. Disponível para todos os usuários com acesso ao workspace.
- Nenhuma atualização automática.
- Opções de layout limitado.
- Não há suporte para consulta nos workspaces do Log Analytics e nos aplicativos do Application Insights.
- As consultas são limitadas no tamanho da resposta para 8 MB e no tempo de execução da consulta de 110 segundos.



## <a name="application-insights-workbooks"></a>Pastas de trabalho do Insights de aplicativos
[As pastas de trabalho](../application-insights/app-insights-usage-workbooks.md) são documentos interativos que fornecem informações detalhadas sobre seus dados, investigação e colaboração dentro da equipe. Exemplos específicos em que as pastas de trabalho são úteis são guias de solução de problemas e postmortem de incidentes.

![Pasta de trabalho](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantagens
- Suporta métricas e logs.
- Suporta parâmetros que permitem relatórios interativos em que a seleção de um elemento em uma tabela atualiza dinamicamente gráficos e visualizações associadas.
- Semelhante a documentos de fluxo.
- Opção para pastas de trabalho pessoais ou compartilhadas.
- Experiência de criação fácil e colaborativa amigável.
- Modelos dão suporte a Galeria de modelos pública baseada no GitHub.

### <a name="limitations"></a>Limitações
- Nenhuma atualização automática.
- Nenhum layout denso, como painéis, o que torna as pastas de trabalho menos úteis como um único painel de vidro. A finalidade mais para fornecer informações mais aprofundadas.


## <a name="power-bi"></a>Power BI
[O Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para criar painéis e relatórios centrados nos negócios, bem como relatórios que analisam tendências de KPI de longo prazo. Você pode [importar os resultados de uma consulta do Log Analytics](../log-analytics/log-analytics-powerbi.md) para um conjunto de dados do Power BI para aproveitar seus recursos, como combinar dados de diferentes origens e compartilhar relatórios na Web e em dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Interatividade extensa, incluindo zoom e filtragem cruzada.
- Fácil de compartilhar em toda a sua organização.
- Integração com outros dados de várias fontes de dados.
- Melhor desempenho com resultados armazenados em cache em um cubo.


### <a name="limitations"></a>Limitações
- Suporta logs, mas não métricas.
- Nenhuma integração do Azure. Não é possível gerenciar painéis e modelos por meio do Azure Resource Manager.
- Os resultados da consulta precisam ser importados para o modelo do Power BI para serem configurados. Limitação no tamanho do resultado e atualização.
- Atualização de dados limitada de oito vezes por dia.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) é uma plataforma aberta que se destaca em painéis operacionais. É particularmente útil para detectar, isolar e triar incidentes operacionais. Você pode adicionar [o plug-in da fonte de dados do Grafana Azure Monitor](../monitoring-and-diagnostics/monitor-send-to-grafana.md) à sua assinatura do Azure para que ela visualize seus dados de métricas do Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Ecossistema rico de origens de dados.
- Dados interatividade incluindo ampliar.
- Dá suporte a parâmetros.

### <a name="limitations"></a>Limitações
- Suporta métricas, mas não registros.
- Nenhuma integração do Azure. Não é possível gerenciar painéis e modelos por meio do Azure Resource Manager.
- Custo para suportar infraestrutura adicional da Grafana ou custo adicional para a Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Crie seu próprio aplicativo personalizado
Você pode acessar dados nas métricas do Azure e no Log Analytics por meio de suas APIs usando qualquer cliente REST, o que permite que você crie seus próprios sites e aplicativos personalizados.

### <a name="advantages"></a>Vantagens
- Flexibilidade total na interface do usuário, visualização, interatividade e recursos.
- Combine métricas e registre dados com outras fontes de dados.

### <a name="disadvantages"></a>Desvantagens
- Esforço significativo de engenharia necessários.


## <a name="next-steps"></a>Próximas etapas
- Aprenda sobre os [dados coletados pelo Azure Monitor](../azure-monitor/platform/data-collection.md).
- Saiba mais sobre os [painéis do Azure](../azure-portal/azure-portal-dashboards.md).
- Saiba mais sobre [modos de exibição no Azure Monitor](../azure-monitor/platform/view-designer.md).
- Saiba mais sobre [pastas de trabalho no Application Insights](../application-insights/app-insights-usage-workbooks.md).
- Saiba mais sobre [importar dados de log no Power BI](../log-analytics/log-analytics-powerbi.md).
- Saiba mais sobre o [plug-in de fonte de dados do Azure Monitor do Grafana](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
