---
title: Visão Geral do Azure Monitor| Microsoft Docs
description: Visão geral dos serviços e funcionalidades da Microsoft que contribuem para uma estratégia de monitoramento completa para seus serviços e aplicativos do Azure.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: bwren
ms.openlocfilehash: d1b7b5baeba88ec0209abff60e9327031afa8b66
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633768"
---
# <a name="azure-monitor-overview"></a>Visão geral do Azure Monitor

O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Visão geral
O diagrama a seguir fornece uma visão geral do Azure Monitor. No centro do diagrama estão os armazenamentos de dados para métricas e logs, que são os dois tipos fundamentais de uso de dados pelo Azure Monitor. À esquerda estão as [fontes que coletam a telemetria de diferentes recursos monitorados](../azure-monitor/platform/data-sources.md) e preenchem os [armazenamentos de dados](../azure-monitor/platform/data-collection.md). À direita estão as diferentes funções que o Azure Monitor executa com os dados coletados, como análise, alertas e transmissão para sistemas externos.


![Visão geral do Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Plataforma de dados de monitoramento
Todos os dados coletados pelo Azure Monitor se enquadram em um dos dois tipos fundamentais, [métricas e logs](../azure-monitor/platform/data-collection.md). As [Métricas](../azure-monitor/platform/data-collection.md#metrics) são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Elas são leves e podem dar suporte a cenários quase em tempo real. Os [Logs](../azure-monitor/platform/data-collection.md#logs) contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os dados telemétricos, como eventos e rastreamentos, são armazenados como logs acrescidos dos dados de desempenho, de modo que possam todos ser combinados para análise.

Para muitos recursos do Azure, você verá os dados coletados pelo Azure Monitor diretamente em sua página de visão geral no portal do Azure. Dê uma olhada em um máquina virtual, por exemplo, e você verá vários gráficos que exibem as métricas de desempenho. Clique em um dos gráficos para abrir os dados no [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md) no portal do Azure, que permite ver os valores de várias métricas no gráfico ao longo do tempo.  É possível exibir os gráficos interativamente ou fixá-los em um painel para exibi-los com outras visualizações.

![Métricas](media/overview/metrics.png)

Os dados de log coletados pelo Azure Monitor são armazenados no Log Analytics, que inclui uma [linguagem de consulta avançada](../log-analytics/log-analytics-queries.md) para recuperar, consolidar e analisar os dados coletados rapidamente.  Você pode criar e testar consultas usando a [página do Log Analytics](../log-analytics/log-analytics-log-search-portals.md) no portal do Azure e analisar os dados diretamente usando essas ferramentas ou salvar consultas para usá-las com [visualizações](visualizations.md) ou [ regras de alerta](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

A linguagem de consulta do Log Analytics é adequada para consultas de log simples, mas também inclui recursos avançados, como agregações, junções e análise inteligente. É possível aprender rapidamente a linguagem de consulta por meio de [várias lições](../log-analytics/query-language/get-started-queries.md) que estão disponíveis.  São fornecidas orientações específicas para usuários que já estão familiarizados com [SQL](../log-analytics/query-language/sql-cheatsheet.md) e [Splunk](../log-analytics/query-language/splunk-cheatsheet.md).

![Logs](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quais dados são coletados pelo Azure Monitor?
O Azure Monitor pode coletar dados de várias fontes. Você pode pensar em dados de monitoramento de seus aplicativos em camadas, que vão do aplicativo, de sistema operacional e serviços de que ele depende, até a própria plataforma. O Azure Monitor coleta dados de cada uma dos seguintes camadas:

- **Dados de monitoramento de aplicativo**: dados sobre o desempenho e a funcionalidade do código que você escreveu, independentemente da plataforma.
- **Dados de monitoramento de SO convidado**: dados sobre o sistema operacional no qual o aplicativo é executado. Ele pode estar em execução no Azure, em outra nuvem ou localmente. 
- **Dados de monitoramento de recursos do Azure**: dados sobre a operação de um recurso do Azure.
- **Dados de monitoramento de assinatura do Azure**: dados sobre a operação e o gerenciamento de uma assinatura do Azure, além de dados sobre a integridade e a operação do próprio Azure. 
- **Dados de monitoramento do inquilino do Azure**: dados sobre a operação de serviços do Azure no nível do locatário, como o Azure Active Directory.

Assim que você cria uma assinatura do Azure e começa a adicionar recursos como máquinas virtuais e aplicativos Web, o Azure Monitor começará a coletar dados.  Os [logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) registram quando os recursos são criados ou modificados. As [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) indicam o desempenho do recurso e os recursos que ele está consumindo. 

Estenda os dados que você está coletando para a operação real dos recursos [habilitando o diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) e [adicionando um agente](../azure-monitor/platform/agent-windows.md) para recursos de computação. Isso colherá dados telemétricos para a operação interna do recurso e permitirá que você configure diferentes [fontes de dados](../azure-monitor/platform/agent-data-sources.md) para coletar logs e métricas do sistema operacional convidado Windows e Linux. 

[Adicione um pacote de instrumentação ao aplicativo](../application-insights/app-insights-azure-web-apps.md) para permitir que o Application Insights colete informações detalhadas sobre seu aplicativo, incluindo exceções, solicitações de aplicativo e exibições de página. Verifique a disponibilidade do aplicativo configurando um [teste de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md) para simular o tráfego de usuários.

### <a name="custom-sources"></a>Fontes personalizadas
O Azure Monitor pode coletar dados de log de qualquer cliente REST usando a [API do Coletor de Dados](../log-analytics/log-analytics-data-collector-api.md). Isso permite que você crie cenários de monitoramento personalizados e estenda o monitoramento a recursos que não expõem a telemetria por outras fontes.



## <a name="insights"></a>Insights
Os dados de monitoramento só serão úteis se puderem aumentar sua visibilidade em relação ao funcionamento do ambiente de computação. O Azure Monitor inclui vários recursos e ferramentas que fornecem insights valiosos sobre seus aplicativos e outros recursos dos quais eles dependem. [Soluções de monitoramento](../azure-monitor/insights/solutions.md) e recursos como o [Application Insights](../application-insights/app-insights-overview.md) e o Container Insights fornecem informações aprofundadas sobre diferentes aspectos do aplicativo e de serviços específicos do Azure. 

### <a name="application-insights"></a>Application Insights
O [Application Insights](../application-insights/app-insights-overview.md) monitora a disponibilidade, o desempenho e o uso do seu aplicativo, seja hospedado na nuvem ou localmente. Ele aproveita a plataforma de análise de dados avançada no Log Analytics para fornecer informações detalhadas sobre as operações do aplicativo e diagnosticar erros sem esperar que um usuário relate-os. O Application Insights inclui pontos de conexão com uma variedade de ferramentas de desenvolvimento e integra-se ao Visual Studio para dar suporte a seus processos de DevOps.

![Insights de aplicativo](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres
O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantados em clusters do Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). Ela oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Os logs do contêiner também são coletados.  Depois de habilitar o monitoramento nos clusters do Kubernetes, essas métricas serão coletadas automaticamente para você por meio de uma versão em contêiner do agente do Log Analytics para Linux e armazenadas no Log Analytics.

![Integridade do contêiner](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
O Azure Monitor para insights de VM monitora suas VMs (máquina virtuais) do Azure em escala analisando o desempenho e a integridade das VMs do Windows e do Linux, incluindo seus diferentes processos e dependências interconectados a outros recursos e processos externos. A solução inclui suporte para monitorar o desempenho e as dependências de aplicativos nas VMs hospedadas localmente ou em outro provedor de nuvem.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluções de monitoramento
As [soluções de gerenciamento](../azure-monitor/insights/solutions.md) no Azure Monitor são conjuntos de lógica empacotados que fornecem informações para determinado aplicativo ou serviço. Eles coletam dados para o Log Analytics junto com outros dados de monitoramento, usando [consultas](../log-analytics/log-analytics-queries.md) para análise e [modos de exibição](../azure-monitor/platform/view-designer.md) para visualização. As soluções de gerenciamento são [disponibilizadas pela Microsoft](../azure-monitor/insights/solutions-inventory.md) e por parceiros para oferecer monitoramento de vários serviços de terceiros e do Azure.

![Soluções de monitoramento](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Respondendo a situações críticas
Além de permitir que você analise interativamente os dados de monitoramento, uma solução de monitoramento eficaz deve ser capaz de responder proativamente a condições críticas identificadas nos dados coletados. Isso pode envolver enviar um texto ou email a um administrador responsável para investigar um problema. Você também pode iniciar um processo automatizado para tentar corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
Os [alertas no Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-alerts.md) notificam proativamente sobre condições críticas e podem tentar tomar as medidas corretivas necessárias. As regras de alerta com base nas métricas fornecem alertas quase em tempo real com base em valores numéricos; já as regras com base nos logs permitem uma lógica complexa entre dados de várias fontes.

As regras de alerta no Azure Monitor usam [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md), que contêm conjuntos exclusivos de destinatários e ações que podem ser compartilhados entre várias regras. De acordo com suas necessidades, os grupos de ação podem executar ações como usar web hooks para fazer com que os alertas iniciem ações externas ou se integrem às ferramentas ITSM.

![Alertas](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
O dimensionamento automático permite ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite que você crie regras que usam métricas coletadas pelo Azure Monitor para determinar quando adicionar recursos automaticamente para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão ociosos. Especifique um número mínimo e máximo de instâncias e a lógica de quando aumentar ou diminuir os recursos.

![Autoscale](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizando dados de monitoramento
As [Visualizações](visualizations.md), como tabelas e gráficos, são ferramentas eficientes para resumir dados de monitoramento e apresentá-los para públicos diferentes. O Azure Monitor tem seus próprios recursos para visualizar os dados de monitoramento e aproveita outros serviços do Azure para mostrá-los a públicos diferentes.

### <a name="dashboards"></a>Painéis
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados, incluindo métricas e logs, em um único painel no [portal do Azure](https://portal.azure.com). Você pode compartilhar o painel com outros usuários do Azure. Podem ser adicionados elementos de todo o Azure Monitor a um painel do Azure, além da saída de qualquer gráfico de métricas ou de consulta de log. Por exemplo, você pode criar um painel que combine blocos que mostram um grafo de métricas, uma tabela de logs de atividades, um gráfico de uso do Application Insights e o resultado de uma consulta no Log Analytics.

![painel](media/overview/dashboard.png)

### <a name="views"></a>Modos de exibição
Os [modos de exibição no Azure Monitor](../azure-monitor/platform/view-designer.md) apresentam dados de log no Log Analytics visualmente.  Cada modo de exibição inclui um único bloco que detalha uma combinação de visualizações, como gráficos de barras e de linhas, além de listas que resumem dados críticos.  As soluções de monitoramento incluem modos de exibição que resumem os dados de um aplicativo específico, e você pode criar seus próprios modos de exibição para apresentar dados de qualquer pesquisa de log do Log Analytics. Assim como outros elementos no Azure Monitor, os modos de exibição podem ser adicionados a painéis do Azure.

![Modo de exibição do Log Analytics](media/overview/view.png)

### <a name="power-bi"></a>Power BI
O [Power BI](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas em uma variedade de fontes de dados e é um meio eficaz de disponibilizar os dados para outras pessoas dentro e fora da sua organização. Você pode configurar o Power BI para [importar dados de log automaticamente do Azure Monitor](../log-analytics/log-analytics-powerbi.md) a fim de aproveitar essas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrar e exportar dados
Geralmente, você terá o requisito de integrar o Azure Monitor a outros sistemas e de criar soluções personalizadas que usam os dados de monitoramento. Outros serviços do Azure funcionam com o Azure Monitor para fornecer essa integração.

### <a name="event-hub"></a>Hub de evento
Os [Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs) são um serviço de ingestão de eventos e plataforma de streaming que pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Use os Hubs de Eventos para [transmitir dados de log do Azure Monitor](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) a ferramentas de monitoramento e SIEM de parceiros.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

### <a name="logic-apps"></a>Aplicativos Lógicos
O [Aplicativo Lógico](https://azure.microsoft.com/services/logic-apps) é um serviço que permite automatizar tarefas e processos de negócios usando fluxos de trabalho que se integram a diversos sistemas e serviços. As atividades disponíveis envolvem ler e gravar logs e métricas no Azure Monitor, o que permite a você criar fluxos de trabalho integrando-se a vários outros sistemas.

![Aplicativo Lógico](../log-analytics/media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="api"></a>API
Várias APIs estão disponíveis para ler e gravar logs e métricas do e para o Azure Monitor, além de acessar os alertas gerados. Você também pode configurar e recuperar alertas. Isso permite possibilidades ilimitadas para criar soluções personalizadas que se integram ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:

* [Métricas e logs](../azure-monitor/platform/data-collection.md) para os dados coletados pelo Azure Monitor.
* [Fontes de dados](../azure-monitor/platform/data-sources.md) para a maneira como os diferentes componentes do seu aplicativo enviam telemetria.
* [Log Analytics](../log-analytics/log-analytics-queries.md) para analisar os dados coletados.
