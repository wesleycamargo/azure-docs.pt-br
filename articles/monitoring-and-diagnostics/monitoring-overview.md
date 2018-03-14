---
title: Monitoramento dos aplicativos e recursos do Azure | Microsoft Docs
description: "Visão geral dos serviços e funcionalidades da Microsoft que contribuem para uma estratégia de monitoramento completa para seus serviços e aplicativos do Azure."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: d8da175a551f7c589c313b2289b2a0209dbd2b56
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitoramento dos aplicativos e recursos do Azure

O monitoramento é o ato de coletar e analisar dados para determinar o desempenho, a integridade e a disponibilidade do seu aplicativo de negócios e os recursos necessários. Uma estratégia de monitoramento eficaz ajuda a esclarecer a operação detalhada dos componentes do seu aplicativo. Também ajuda a aumentar o tempo de atividade, notificando sobre questões críticas proativamente para que você possa resolvê-las antes que se tornem problemas.

O Azure inclui vários serviços que individualmente executam uma função ou tarefa específica no espaço de monitoramento. Juntos, esses serviços oferecem uma solução abrangente para coletar, analisar e agir na telemetria do seu aplicativo e os recursos do Azure compatíveis com eles. Eles também podem trabalhar para monitorar recursos críticos locais para fornecer um ambiente de monitoramento híbrido. Compreender as ferramentas e os dados que estão disponíveis é a primeira etapa no desenvolvimento de uma estratégia de monitoramento completa para seu aplicativo. 

O diagrama a seguir mostra uma exibição conceitual dos componentes que funcionam em conjunto para fornecer monitoramento dos recursos do Azure. As seguintes seções descrevem esses componentes e fornecem links para as informações técnicas detalhadas.

![Visão geral de monitoramento](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Monitoramento básico
O monitoramento básico fornece monitoramento fundamental, necessário nos recursos do Azure. Esses serviços requerem configuração mínima e coletam a telemetria fundamental utilizada pelos serviços de monitoramento Premium.    

### <a name="azure-monitor"></a>Azure Monitor
O [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) habilita o monitoramento básico para o serviço do Azure, permitindo a coleta de [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e [logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Por exemplo, o log de atividades informará quando novos recursos forem criados ou modificados. 

Métricas disponíveis que fornecem estatísticas de desempenho para diferentes recursos e até mesmo o sistema operacional dentro de uma máquina virtual. É possível exibir esses dados com um dos exploradores no Portal do Azure, enviá-los ao Azure Log Analytics para análise detalhada e de tendências, ou criar regras de alerta para informá-lo proativamente sobre problemas críticos.

### <a name="service-health"></a>Integridade do Serviço
A integridade do aplicativo depende dos serviços do Azure necessários ao aplicativo. A [Integridade do Serviço do Azure](../service-health/service-health-overview.md) identifica os problemas com os serviços do Azure que possam afetar seu aplicativo. A Integridade do Serviço também ajuda você a planejar para a manutenção agendada.

### <a name="azure-advisor"></a>Azure Advisor
O [Assistente do Azure](../advisor/advisor-overview.md) monitora constantemente sua telemetria de uso e configuração de recursos. Ele fornece recomendações personalizadas com base nas práticas recomendadas. Seguir essas recomendações irá ajudá-lo a melhorar o desempenho, a segurança e a disponibilidade dos recursos compatíveis com seus aplicativos.


## <a name="premium-monitoring-services"></a>Serviços de monitoramento Premium
Os seguintes serviços do Azure fornecem recursos avançados para coletar e analisar dados de monitoramento. Esses serviços são criados com base em monitoramento básico e tiram proveito da funcionalidade comum no Azure. Eles fornecem uma análise poderosa com os dados coletados para fornecer informações exclusivas sobre seus aplicativos e sua infraestrutura. Além disso, apresentam dados no contexto de cenários destinados a públicos diferentes.

### <a name="application-insights"></a>Application Insights
Você pode usar o [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) para monitorar a disponibilidade, o desempenho e o uso do seu aplicativo, hospedado na nuvem ou localmente. 

Ao instrumentar seu aplicativo para trabalhar com o Application Insights, você pode obter informações muito detalhadas. Você pode então identificar e diagnosticar erros rapidamente sem esperar que um usuário os relate. Com as informações coletadas, será possível fazer as escolhas informadas sobre a manutenção e as melhorias do seu aplicativo. 

O Application Insights tem ferramentas abrangentes para interagir com os dados que coleta. O Application Insights armazena seus dados em um repositório comum. Ele pode tirar proveito de recursos compartilhados, como alertas, painéis e análise detalhada com a linguagem de consulta do Log Analytics.

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) desempenha uma função central na monitoração do Azure coletando dados de uma variedade de recursos em um único repositório. Lá, você pode analisar os dados usando uma linguagem de consulta avançada. 

O Application Insights e a Central de Segurança do Azure armazenam seus dados no armazenamento de dados do Log Analytics e utilizam o mecanismo de análise. Os dados são dados coletados do Azure Monitor, as soluções de gerenciamento e os agentes instalados nas máquinas virtuais na nuvem ou localmente. Essa funcionalidade compartilhada ajuda a formar uma imagem completa do seu ambiente. 


### <a name="service-map"></a>Mapa do Serviço
O [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) fornece informações sobre o seu ambiente de IaaS, analisando máquinas virtuais com seus diferentes processos e dependências em outros computadores e processos externos. Ele integra soluções de gerenciamento, eventos e dados de desempenho ao Log Analytics. Você pode então exibir esses dados no contexto de cada computador e sua relação com o restante do seu ambiente. 

O Mapa do Serviço é similar ao [Mapa de Aplicativos no Application Insights](../application-insights/app-insights-app-map.md). Ele aborda os componentes de infraestrutura compatíveis com seus aplicativos.

### <a name="network-watcher"></a>Observador de Rede
O [Observador de Rede](../network-watcher/network-watcher-monitoring-overview.md) fornece diagnóstico e monitoramento baseado em cenários para diferentes cenários de rede no Azure. Ele armazena dados de métricas e diagnósticos do Azure para análise posterior. Ele trabalha com as seguintes soluções para monitorar vários aspectos da sua rede:
* [Monitor de Desempenho de Rede (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/): uma solução de monitoramento de rede com base em nuvem que monitora a conectividade em várias nuvens públicas, data centers e ambientes locais.
* [Monitor do ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/): um recurso NPM que monitora a conectividade de ponta a ponta e o desempenho em circuitos do Azure ExpressRoute.
* Análise de Tráfego: uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo na rede em nuvem.
* [Análise de DNS](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns): uma solução que fornece insights relacionados a segurança, desempenho e operações, com base nos servidores DNS.

### <a name="management-solutions"></a>Soluções de gerenciamento
As [Soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) são conjuntos de lógica empacotados que fornecem informações para um aplicativo ou serviço específico. Elas dependem do Log Analytics para armazenar e analisar os dados de monitoramento que coletam. 

As soluções de gerenciamento estão disponíveis a partir da Microsoft e de parceiros para fornecer monitoramento a vários serviços de terceiros e do Azure. São exemplos de soluções de monitoramento:
* [Monitoramento de Contêiner](../log-analytics/log-analytics-containers.md), que ajuda a exibir e gerenciar seus hosts de contêiner.
* [Análise de SQL do Azure](../log-analytics/log-analytics-azure-sql.md), que coleta e visualiza as métricas de desempenho para bancos de dados de SQL do Azure.


## <a name="shared-functionality"></a>Funcionalidade compartilhada
As ferramentas do Azure a seguir fornecem funcionalidades críticas para os serviços de monitoramento Premium. Vários serviços compartilham delas, permitindo que você aproveite a funcionalidade e as configurações comuns nos diversos serviços.

### <a name="alerts"></a>Alertas
Os [alertas do Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) notificam proativamente sobre condições críticas e potencialmente tomam as medidas corretivas necessárias. As regras de alerta podem utilizar dados de várias fontes, incluindo métricas e logs. Elas usam os [grupos de ações](../monitoring-and-diagnostics/monitoring-action-groups.md) que contêm conjuntos exclusivos de destinatários e as ações em resposta a um alerta. Com base nas suas necessidades, é possível ter alertas para inicializar ações externas usando webhooks e integrar suas ferramentas de ITSM.

### <a name="dashboards"></a>Painéis
Você pode usar os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) para combinar diferentes tipos de dados em um único painel no Portal do Azure. Em seguida, você pode compartilhar o painel com outros usuários do Azure. 

Por exemplo, você pode criar um painel que combina:
- Blocos que mostram o gráfico de métricas
- Uma tabela de logs de atividades
- Uma tabela de uso do Application Insights
- A saída de uma pesquisa de logs no Log Analytics

Você também pode exportar dados do Log Analytics para o [Power BI](https://docs.microsoft.com/power-bi/). Lá, você pode tirar proveito das visualizações adicionais. Você também pode tornar os dados disponíveis para outras pessoas dentro e fora da sua organização.

### <a name="metrics-explorer"></a>Metrics Explorer
As [Métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) são valores numéricos gerados por um recurso do Azure para ajudar a esclarecer a operação e o desempenho do recurso. Usando o Metrics Explorer, você pode enviar métricas aos Log Analytics para análise com dados de outras fontes.



### <a name="activity-log"></a>Log de Atividade
O [Log de Atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) fornece dados sobre a operação de um recurso do Azure. Essas informações incluem:
- Alterações na configuração do recurso.
- Incidentes de integridade do serviço.
- Recomendações sobre como usar melhor o recurso.
- Informações relacionadas às operações de dimensionamento automático. 

Você pode exibir os logs de um recurso específico na respectiva página no portal do Azure. Ou você pode exibir logs de vários recursos no Explorador do Log de Atividades. 

Você também pode enviar logs de atividade para o Log Analytics. Lá, você pode analisar os logs usando os dados coletados por soluções de gerenciamento, agentes em máquinas virtuais e outras fontes.


## <a name="example-scenarios"></a>Cenários de exemplo
A seguir, são apresentados exemplos de alto nível que ilustram como é possível utilizar as diferentes ferramentas de monitoramento no Azure para diferentes cenários.

### <a name="monitoring-a-web-application"></a>Monitorando um aplicativo Web
Considere um aplicativo Web implantado no Azure por meio do Serviço de Aplicativo do Azure, Armazenamento do Microsoft Azure e um banco de dados SQL. Comece acessando [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) para esses recursos nas páginas do portal do Azure. Procure informações críticas, como o número de solicitações para o aplicativo e o tempo médio de resposta. Você também pode identificar alterações de configuração.

Em seguida, acesse o Monitor no portal para exibir as métricas e os logs dos diferentes recursos em conjunto. Ao determinar os parâmetros padrão para as métricas, [crie regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Essas regras notificam proativamente quando, por exemplo, o tempo médio de resposta aumenta excedendo um limite. Para obter uma exibição rápida do desempenho diário do aplicativo, você cria um painel do Azure para mostrar grafos de métricas que representam KPI críticos.

Para realizar um monitoramento profundo do aplicativo, você [configura-o para o Application Insights](../application-insights/quick-monitor-portal.md). Agora, será possível coletar dados adicionais que fornecem informações adicionais sobre a operação e o desempenho do aplicativo. O Application Insights detecta as relações subjacentes entre os componentes do aplicativo. Ele possibilita a representação visual por meio do [Mapa do Aplicativo](../application-insights/app-insights-app-map.md) juntamente com o [rastreamento ponta a ponta](../application-insights/app-insights-transaction-diagnostics.md) para diagnosticar a dependência, exceção ou componente exato onde ocorreu um problema. 

Você cria [Testes de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md) para testar proativamente o aplicativo de diferentes regiões. Para ajudar os desenvolvedores, você [habilita o Profiler](../application-insights/enable-profiler-compute.md), de modo que seja possível rastrear solicitações e quaisquer exceções para uma linha de código específica. Para obter uma maior visibilidade nos serviços utilizados no aplicativo, você adiciona a [solução SQL Analytics](../log-analytics/log-analytics-azure-sql.md) para coletar dados adicionais no Log Analytics. 

Após algum tempo, você decide investigar a causa raiz de períodos quando o desempenho no site ficou abaixo de um limite. Você pode gravar uma consulta usando o Log Analytics. Ele ajuda a correlacionar os dados de uso e desempenho coletados pelo Application Insights com dados de configuração e desempenho nos recursos do Azure que dão suporte ao aplicativo.


### <a name="monitoring-virtual-machines"></a>Monitoramento de máquinas virtuais
Você possui uma combinação de máquinas virtuais do Windows e Linux que funcionam no Azure. Use o Azure Monitor para exibir [logs de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e [métricas de nível do host](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Adicione a [extensão do Diagnóstico do Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) às máquinas virtuais para coletar métricas do sistema operacional convidado. Em seguida, você cria as [regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para notificá-lo de forma proativa quando exceder as métricas básicas, como a utilização do processador e os limites na memória.

Para coletar mais detalhes sobre máquinas virtuais que executam um aplicativo de negócios, você [cria um espaço de trabalho do Log Analytics e habilita a extensão da VM](../log-analytics/log-analytics-quick-collect-azurevm.md) em cada computador. Você configura a [coleção de fontes de dados diferentes](../log-analytics/log-analytics-data-sources.md) para o aplicativo e [cria exibições](../log-analytics/log-analytics-view-designer.md) para relatar sobre a operação e o desempenho diários. Em seguida, você [cria regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para notificá-lo quando eventos de erro específicos forem recebidos. 

Para monitorar continuamente a integridade do agente instalado, adicione a [solução de gerenciamento de Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md). Para obter mais informações sobre o aplicativo, [adicione o agente de dependência ](../operations-management-suite/operations-management-suite-service-map-configure.md) às máquinas virtuais para adicioná-las ao [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md). O Mapa do Serviço descobre processos críticos e identifica as conexões entre computador com outros serviços. 

Após uma interrupção relatada, você usará o Mapa do Serviço para executar análises e identificar os computadores específicos que experimentaram o problema. Em seguida, crie um [consulta nos dados do Log Analytics](../log-analytics/log-analytics-log-search-new.md) para identificar o problema no futuro. E crie uma regra de alerta para notificar proativamente quando a condição for detectada.



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:

* [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977).
* [Introdução ao Azure Monitor](monitoring-get-started.md).
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu serviço de nuvem, máquina virtual, conjunto de dimensionamento de máquinas virtuais ou aplicativo do Azure Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você estiver tentando diagnosticar problemas em seu aplicativo Web do Serviço de Aplicativo.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) para analisar os dados de monitoramento coletados.
