---
title: Monitoramento dos aplicativos e recursos do Azure | Microsoft Docs
description: "Visão geral dos diferentes serviços e funcionalidades da Microsoft que contribuem para uma estratégia de monitoramento completa para seus serviços e aplicativos do Azure."
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
ms.openlocfilehash: 505e92b5fc63f570bc4d0f8899ae977b93850356
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitoramento dos aplicativos e recursos do Azure

O monitoramento é o ato de coletar e analisar dados para determinar o desempenho, a integridade e a disponibilidade do seu aplicativo de negócios e os recursos necessários. Uma estratégia de monitoramento efetiva irá ajudá-lo a entender a operação detalhada dos diferentes componentes do aplicativo e a aumentar o tempo de atividade, notificando-o proativamente sobre problemas críticos para que possa resolvê-los antes de se tornarem problemas.

O Azure inclui vários serviços que desempenham individualmente uma função ou tarefa específica no espaço de monitoramento e, em conjunto, entrega uma solução abrangente para coletar, analisar e atuar em telemetria do aplicativo e recursos do Azure subjacentes e fornecendo suporte.  Eles também podem trabalhar para monitorar recursos críticos locais para fornecer um ambiente de monitoramento híbrido.   Compreender as ferramentas e os dados que estão disponíveis é a primeira etapa no desenvolvimento de uma estratégia de monitoramento completa para seu aplicativo. 

O diagrama a seguir mostra uma exibição conceitual dos diferentes componentes que funcionam em conjunto para fornecer monitoramento dos recursos do Azure.  Cada um está descrito nas seções a seguir e com links para as informações técnicas detalhadas.

![Visão geral de monitoramento](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Monitoramento básico
O monitoramento básico fornece monitoramento fundamental necessário nos recursos do Azure.  Esses serviços requerem configuração mínima e coletam a telemetria fundamental que é alavancada pelos serviços de monitoramento Premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) habilita o monitoramento básico para o serviço do Azure, permitindo a coleta de [Métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [Logs de Atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e [Logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Por exemplo, o log de atividades informará quando novos recursos forem criados ou modificados.  Métricas disponíveis que fornecem estatísticas de desempenho para diferentes recursos e até mesmo o sistema operacional dentro de uma máquina virtual.  É possível exibir esses dados com um dos exploradores no Portal do Azure, enviá-los ao Log Analytics para análise detalhada e de tendências, ou criar regras de alerta para informá-lo proativamente sobre problemas críticos.

### <a name="service-health"></a>Integridade do Serviço
A integridade do aplicativo depende dos serviços do Azure necessários ao aplicativo.  A [Integridade do Serviço do Azure](../service-health/service-health-overview.md) identifica quaisquer problemas com os serviços do Azure que possam afetar o aplicativo e ajuda-o a planejar qualquer manutenção de programação.

### <a name="azure-advisor"></a>Azure Advisor
O [Assistente do Azure](../advisor/advisor-overview.md) monitora constantemente a configuração de recursos e a telemetria de uso para fornecer recomendações personalizadas com base nas melhores práticas.  Seguir essas recomendações irão ajudá-lo a melhorar o desempenho, a segurança e a disponibilidade dos recursos fornecendo suporte a seus aplicativos.


## <a name="premium-monitoring-services"></a>Serviços de monitoramento Premium
Os seguintes serviços do Azure fornecem recursos avançados para coletar e analisar dados de monitoramento.  Eles compilam o monitoramento básico, aproveitam a funcionalidade comum no Azure e fornecem análises avançadas com os dados coletados para proporcionar percepções exclusivas aos seus aplicativos e infraestrutura.  Além disso, apresentam dados no contexto de cenários específicos destinados a públicos diferentes.

### <a name="application-insights"></a>Application Insights
O [Application Insights](http://azure.microsoft.com/documentation/services/application-insights) permite que você monitore a disponibilidade, o desempenho e o uso do seu aplicativo, hospedado na nuvem ou localmente.  Ao instrumentar o aplicativo para trabalhar com o Application Insights, você poderá obter percepções detalhadas que permitirá identificar e diagnosticar os erros de maneira rápida, sem aguardar que um usuário relate-os. Com as informações coletadas, será possível fazer as escolhas informadas sobre a manutenção e as melhorias do seu aplicativo.  Além das ferramentas extensivas para interagir com os dados coletados, o Application Insights armazena seus dados em um repositório comum para aproveitar as funcionalidades compartilhadas como alertas, painéis e análises profundas com a linguagem de consulta do Log Analytics.

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) desempenha uma função central na monitoração do Azure coletando dados de uma variedade de recursos em um único repositório, onde poderá ser analisado com uma linguagem de consulta avançada.  O Application Insights e a Central de Segurança do Azure armazenam seus dados no armazenamento de dados do Log Analytics e utilizam o mecanismo de análise.  Isso, combinado com os dados coletados do Azure Monitor, as soluções de gerenciamento e os agentes instalados nas máquinas virtuais na nuvem ou localmente, permite que você forme uma imagem completa de todo o seu ambiente. 


### <a name="service-map"></a>Mapa do Serviço
O [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) fornece informações sobre o seu ambiente de IaaS, analisando máquinas virtuais com seus diferentes processos e dependências em outros computadores e processos externos.  Ele integra eventos, dados de desempenho e soluções de gerenciamento no Log Analytics para que você possa exibir esses dados no contexto de cada computador e a relação com o restante do seu ambiente.  O Mapa do Serviço é semelhante ao [mapa do aplicativo no Application Insights](../application-insights/app-insights-app-map.md), mas concentra-se nos componentes da infraestrutura que dão suporte aos aplicativos.

### <a name="network-watcher"></a>Observador de Rede
O [Observador de Rede](../network-watcher/network-watcher-monitoring-overview.md) fornece diagnóstico e monitoramento baseado em cenários para diferentes cenários de rede no Azure.  Ele armazena dados em métricas e diagnósticos do Azure para análise posterior e trabalha com as seguintes soluções de monitoramento de rede para monitorar diversos aspectos da rede:
* [Monitor de Desempenho de Rede (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) - uma solução de monitoramento de rede com base em nuvem que monitora a conectividade em várias nuvens públicas, data centers e ambientes locais
* [Monitor do ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) -um recurso NPM que monitora a conectividade de ponta a ponta e o desempenho em circuitos do ExpressRoute.
* Análise de Tráfego - uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo na rede em nuvem.
* [Análise de DNS](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns) -fornece insights relacionados a segurança, desempenho e operações, com base nos servidores DNS.

### <a name="management-solutions"></a>Soluções de gerenciamento
As [Soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) são conjuntos de lógica empacotados que fornecem informações para um aplicativo ou serviço específico.  Elas dependem do Log Analytics para armazenar e analisar os dados de monitoramento que coletam.  As soluções de gerenciamento estão disponíveis a partir da Microsoft e de parceiros que fornecem monitoramento a vários serviços de terceiros e do Azure. O exemplo de soluções de monitoramento inclui o [Monitoramento de Contêiner](../log-analytics/log-analytics-containers.md) que ajuda-o a exibir e gerenciar seus hosts de contêineres e a [Análise de SQL do Azure](../log-analytics/log-analytics-azure-sql.md) que coleta e visualiza métricas de desempenho para bancos de dados do SQL Azure.


## <a name="shared-functionality"></a>Funcionalidade compartilhada
As ferramentas do Azure a seguir fornecem funcionalidades críticas para os serviços de monitoramento Premium.  Essas ferramentas são compartilhadas por vários serviços, permitindo que você aproveite a funcionalidade e as configurações comuns nos diversos serviços.

### <a name="alerts"></a>Alertas
Os [Alertas do Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) notificam proativamente as condições críticas e as potenciais medidas corretivas a serem tomadas.  As regras de alerta podem aproveitar dados de várias fontes, incluindo métricas e logs. Elas usam os [Grupos de Ações](../monitoring-and-diagnostics/monitoring-action-groups.md) que contêm conjuntos exclusivos de destinatários e as ações em resposta a um alerta.  Com base nas suas necessidades, é possível ter alertas para inicializar ações externas usando webhooks e integrar suas ferramentas de ITSM.

### <a name="dashboards"></a>Painéis
Os [Painéis do Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados em um único painel no Portal do Azure e compartilhar com outros usuários do Azure.  Por exemplo, você pode criar um painel que combine blocos mostrando um grafo de métricas, uma tabela de logs de atividades, um gráfico de uso do Application Insights e a resposta de uma pesquisa de logs no Log Analytics.

Adicionalmente, é possível exportar dados do Log Analytics ao [Power BI](https://docs.microsoft.com/power-bi/) para usufruir das visualizações adicionais e disponibilizar os dados a outros usuários dentro e fora de sua organização.

### <a name="metrics-explorer"></a>Metrics Explorer
As [Métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) são valores numéricos gerados pelos recursos do Azure que ajudam a entender a operação e o desempenho do recurso. Você pode enviar métricas aos Log Analytics para análise com dados de outras fontes.



### <a name="activity-logs"></a>Logs de atividade
Os [Logs de Atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) fornecem dados sobre a operação dos recursos do Azure.  Isso inclui informações como alterações de configuração para o recurso, incidentes de integridade do serviço, recomendações sobre melhor utilização do recurso e informações relacionadas às operações de autoescala.  Você pode exibir logs para um recurso específico em sua página no Portal do Azure ou exibir logs de vários recursos no Explorador do Log de Atividades.  Também é possível enviar logs de atividades para o Log Analytics de modo que possam ser analisados com dados coletados pelas soluções de gerenciamento, por agentes em máquinas virtuais e outras fontes.


## <a name="example-scenarios"></a>Cenários de exemplo
A seguir, são apresentados exemplos de alto nível que ilustram como é possível aproveitar as diferentes ferramentas de monitoramento no Azure para diferentes cenários.

### <a name="monitoring-a-web-application"></a>Monitorando um aplicativo Web
Considere um aplicativo Web implantado no Azure usando os Serviços de Aplicativos, Armazenamento do Microsoft Azure e um banco de dados SQL.  Você pode iniciar, acessando [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) para cada um desses recursos individuais nas páginas no Portal do Azure.  Isso inclui informações críticas, como a quantidade de solicitações para o aplicativo e o tempo médio de resposta, além de identificar quaisquer alterações de configuração.

Em seguida, você pode acessar Monitorar no portal para exibir as métricas e os logs dos diferentes recursos em conjunto.  À medida que determina os parâmetros padrão para as métricas, você [cria regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para notificá-lo proativamente quando, por exemplo, o tempo médio de resposta exceder um limite.  Para obter uma exibição rápida do desempenho diário do aplicativo, você cria um painel do Azure para mostrar grafos de métricas que representam KPI críticos.

Para realizar um monitoramento profundo do aplicativo, você [configura-o para o Application Insights](../application-insights/quick-monitor-portal.md).  Agora, será possível coletar dados adicionais, fornecendo as informações adicionais sobre a operação e o desempenho do aplicativo.  O Application Insights detecta as relações subjacentes entre os componentes do seu aplicativo, permitindo a representação visual por meio do [Mapa do Aplicativo](../application-insights/app-insights-app-map.md) juntamente com o [rastreamento ponta a ponta](../application-insights/app-insights-transaction-diagnostics.md) para diagnosticar a dependência, exceção ou componente exato onde ocorreu um problema.  Você cria [Testes de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md) para testar proativamente o aplicativo de diferentes regiões.  Para ajudar os desenvolvedores, você [habilita o Profiler](../application-insights/enable-profiler-compute.md), de modo que seja possível rastrear solicitações e quaisquer exceções para uma linha de código específica.  

Para obter uma maior visibilidade nos serviços utilizados no aplicativo, você adiciona a [solução SQL Analytics](../log-analytics/log-analytics-azure-sql.md) para coletar dados adicionais no Log Analytics. Após algum tempo, você decide investigar a causa raiz de períodos de tempo quando o desempenho no site ficou abaixo do limite.  Você grava uma consulta usando o Log Analytics para correlacionar os dados de uso e desempenho coletados pelo Application Insights com dados de configuração e desempenho nos recursos do Azure que dão suporte ao aplicativo.


### <a name="monitoring-virtual-machines"></a>Monitoramento de máquinas virtuais
Você possui uma combinação de máquinas virtuais do Windows e Linux que funcionam no Azure.  Você usa o Azure Monitor para exibir os [logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e as [métricas em nível de host](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e, em seguida, adiciona a [extensão do Diagnóstico do Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) às máquinas virtuais para coletar métricas do sistema operacional convidado.  Em seguida, você cria as [regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para notificá-lo de forma proativa quando exceder as métricas básicas, como a utilização do processador e os limites na memória.

Para coletar mais detalhes sobre máquinas virtuais que executam um aplicativo de negócios, você [cria um espaço de trabalho do Log Analytics e habilita a extensão da VM](../log-analytics/log-analytics-quick-collect-azurevm.md) em cada computador.  Você configura a [coleção de fontes de dados diferentes](../log-analytics/log-analytics-data-sources.md) para o aplicativo e [cria exibições](../log-analytics/log-analytics-view-designer.md) para relatar sobre a operação e o desempenho diários.  Em seguida, você [cria regras de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para notificá-lo quando eventos de erro específicos forem recebidos.  Para monitorar continuamente a integridade do agente instalado, adicione a [solução de gerenciamento de Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md).

Para obter mais informações sobre o aplicativo [adicione o agente de dependência ](../operations-management-suite/operations-management-suite-service-map-configure.md) às máquinas virtuais para adicioná-las ao [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md).  Ele descobre processos críticos e identifica as conexões entre computador com outros serviços.  Após uma interrupção relatada, você usará o Mapa do Serviço para executar análises e identificar os computadores específicos que experimentaram o problema.  Em seguida, crie uma [consulta nos dados do Log Analytics](../log-analytics/log-analytics-log-search-new.md) para identificar o problema no futuro e, crie uma regra de alerta para informá-lo proativamente quando a condição for detectada.



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

* [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introdução ao Azure Monitor](monitoring-get-started.md)
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual, conjunto de dimensionamento de máquinas virtuais ou aplicativo do Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) para analisar os dados de monitoramento coletados.
