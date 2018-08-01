---
title: Coletando dados de monitoramento no Azure | Microsoft Docs
description: Visão geral dos dados de monitoramento coletados do aplicativo e dos serviços no Azure e as ferramentas usadas para analisá-los.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213292"
---
# <a name="collecting-monitoring-data-in-azure"></a>Coletando dados de monitoramento no Azure
Este artigo fornece uma visão geral dos dados de monitoramento coletados do aplicativo e dos serviços no Azure e as ferramentas usadas para analisá-los. 

## <a name="types-of-monitoring-data"></a>Tipos de dados de monitoramento
Todos os dados de monitoramento se encaixam em um de dois tipos fundamentais, métricas ou logs. Cada tipo tem diferentes características e é mais adequado para cenários específicos, conforme descrito abaixo.

### <a name="metrics"></a>Métricas
Métricas são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Elas incluem diferentes dados, inclusive o próprio valor, a hora em que o valor foi coletado, o tipo de medida que o valor representa e o recurso específico ao qual o valor está associado. As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Por exemplo, você pode coletar a utilização do processador de uma máquina virtual a cada minuto ou o número de usuários conectados ao seu aplicativo a cada 10 minutos.

As métricas são leves e capazes de dar suporte a cenários quase em tempo real. Elas são especialmente úteis para alertar, uma vez que podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples. Por exemplo, você poderá disparar um alerta quando uma métrica exceder um valor limite ou quando a diferença entre o valor de duas métricas atingir um determinado valor.

Métricas individuais normalmente fornecem pouco insight sozinhas. Elas fornecem um único valor sem nenhum contexto diferente da comparação com um limite simples. São valiosas quando combinadas com outras métricas para identificar padrões e tendências ou quando combinadas com logs que fornecem contexto sobre determinados valores. Por exemplo, um determinado número de usuários em seu aplicativo em um determinado momento pode lhe dizer pouco sobre a integridade do aplicativo. Uma queda repentina nos usuários indicados por vários valores da mesma métrica pode indicar um problema. Exceções excessivas geradas pelo aplicativo e indicadas por uma métrica separada podem identificar um problema do aplicativo que causa a queda. Eventos criados pelo aplicativo que identificam uma falha em componentes específicos do aplicativo podem auxiliar você a identificar a causa raiz.

Alertas com base em logs não são tão responsivos quanto alertas com base em métricas, mas podem incluir uma lógica mais complexa. É possível criar um alerta com base nos resultados de uma consulta que realiza análise complexa sobre dados de várias fontes.

### <a name="logs"></a>Logs
Os logs contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os logs podem conter valores numéricos como métricas, mas normalmente contêm dados de texto com descrições detalhadas. Além disso, eles são diferentes das métricas, porque variam em sua estrutura e geralmente não são coletados em intervalos regulares.

Um tipo comum de entrada de log é um evento. Os eventos coletados esporadicamente enquanto são criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer um contexto completo por conta própria.  Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

Logs são especialmente úteis para combinar dados de uma variedade de fontes para obter análise complexa e tendências ao longo do tempo. Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. As métricas podem até mesmo ser replicadas em logs para combiná-los com outros dados de monitoramento para tendências e outras análises de dados.


## <a name="monitoring-tools-in-azure"></a>Ferramentas de monitoramento no Azure
Os dados de monitoramento no Azure são coletados e analisados usando várias fontes descritas nas seguintes seções.

### <a name="azure-metrics"></a>Métricas do Azure
As métricas dos recursos e aplicativos do Azure são coletadas para Métricas do Azure. Os dados de métrica são integrados às páginas no portal do Azure para recursos específicos do Azure, como máquinas virtuais, que incluem grafos dessas métricas como utilização da CPU e da rede para o computador selecionado. Eles também podem ser analisados com o [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md) que podem conter os valores de várias métricas ao longo do tempo.  É possível exibir os gráficos interativamente ou fixá-los em um painel para exibi-los com outras visualizações. Também é possível recuperar métricas com a [API REST de monitoramento do Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

É possível obter detalhes sobre os dados de métrica coletados por diferentes tipos de recursos do Azure em [Sources of monitoring data in Azure](monitoring-data-sources.md) (Fontes de dados de monitoramento no Azure). 

![Metrics Explorer](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Log de Atividades do Azure 
O [Log de atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) armazena logs sobre a configuração e a integridade dos serviços do Azure. É possível usar o Explorador do Log de Atividades para exibir esses logs no portal do Azure, mas eles são comumente [copiados para o Log Analytics](../log-analytics/log-analytics-activity.md) para serem analisados com outros dados de log.

É possível usar o Explorador do Log de Atividades para exibir o log de atividades filtrado para corresponder a determinados critérios.  A maioria dos recursos também terá uma opção Log de atividades em seu menu no portal do Azure que exibe o Explorador do Log de Atividades filtrado para esse recurso. Também é possível recuperar Logs de atividades com a [API REST de monitoramento do Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Explorador do Log de Atividades](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
O Log Analytics fornece uma plataforma de dados comuns para gerenciamento no Azure. É o serviço principal usado para armazenamento e análise de logs no Azure, coletar dados de uma variedade de fontes, incluindo agentes em máquinas virtuais, soluções de gerenciamento e diferentes recursos do Azure. Dados de outras fontes, incluindo métricas e o Log de atividades, podem ser copiados para o Log Analytics a fim de criar um repositório central completo de dados de monitoramento.

O Log Analytics tem uma avançada linguagem de consulta para analisar os dados coletados.  É possível usar [portais de pesquisa de logs](../log-analytics/log-analytics-log-search-portals.md) para escrever e testar interativamente consultas e analisar seus resultados. Também é possível [criar modos de exibição](../log-analytics/log-analytics-view-designer.md) para visualizar os resultados de pesquisas de logs ou colar os resultados de uma consulta diretamente em um painel do Azure.  As soluções de gerenciamento incluem pesquisas de logs e exibições no Log Analytics para analisar os dados coletados. Outros serviços como o Application Insights armazenam dados no Log Analytics e oferecem mais ferramentas para análise.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
O Application Insights coleta a telemetria de aplicativos Web instalados em uma variedade de plataformas. Ele armazena seus dados nas Métricas do Azure e no Log Analytics e oferece um conjunto extensivo de ferramentas avançadas, além das ferramentas existentes para analisar esses dados, para analisar e visualizar seus dados. Isso permite que você use um conjunto comum de serviços como alertas, pesquisas de logs e painéis usados para outro monitoramento.


![Insights de aplicativo](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Mapa do Serviço
O Mapa do Serviço oferece uma representação visual de máquinas virtuais com seus processos e dependências. Ele armazena a maioria desses dados no Log Analytics para que você possa analisá-los com outros dados de gerenciamento. O console do Mapa do Serviço também recupera dados do Log Analytics para apresentá-los no contexto da máquina virtual que está sendo analisada.

![Mapa do Serviço](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Transferindo dados de monitoramento

### <a name="metrics-to-logs"></a>Métricas para logs
As métricas também podem ser replicadas para o Log Analytics para realizar uma análise complexa com outros tipos de dados usando sua avançada linguagem de consulta. Também é possível reter dados de log para períodos mais longos do que métricas, o que permite realizar as tendências com o tempo. Quando métricas ou qualquer outros dados de desempenho são armazenados no Log Analytics, esses dados agem como um log. Use métricas para dar suporte a análise quase e tempo real e a alertas ao usar logs para obter tendências e análise com outros dados.

É possível obter orientação para coletar métricas de recursos do Azure em [Coletar logs e as métricas do serviço do Azure para uso no Log Analytics](../log-analytics/log-analytics-azure-storage.md). Obtenha orientação para coletar métricas de recursos dos recursos de PaaS do Azure em [Coletar métricas de recursos de PaaS do Azure com Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Logs para métricas
Conforme descrito acima, métricas são mais responsivas do que logs, o que permite que você crie alertas com latência menor e a um custo mais baixo. O Log Analytics coleta uma quantidade significativa de dados numéricos que seriam adequados para as métricas, mas não são armazenados nas Métricas do Azure. Um exemplo comum são os dados de desempenho coletados de agentes e de soluções de gerenciamento. Alguns desses valores podem ser copiados para as Métricas do Azure, em que estão disponíveis para alertas e para análise com o Metrics Explorer.

A explicação desse recurso está disponível em [Faster Metric Alerts for Logs now in limited public preview](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/) (Alertas de métrica mais rápidos para logs agora em visualização pública limitada). A lista de suporte de valores está disponível em [Supported metrics and creation methods for new metric alerts](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md) (Métricas e métodos de criação com suporte para novos alertas de métrica).

### <a name="event-hub"></a>Hub de evento
Além de usar as ferramentas no Azure para analisar os dados de monitoramento, convém encaminhá-los para uma ferramenta externa como um produto SIEM (gerenciamento de eventos e informações de segurança). Normalmente, isso é feito usando o [Hub de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/). 

É possível obter orientação para os diferentes tipos de dados de monitoramento em [Stream Azure monitoring data to an event hub for consumption by an external tool](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) (Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [dados de monitoramento disponíveis](monitoring-data-sources.md) para diferentes recursos no Azure. 