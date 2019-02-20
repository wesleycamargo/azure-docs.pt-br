---
title: Dados de monitoramento coletados pelo Azure Monitor | Microsoft Docs
description: Os dados de monitoramento coletados pelo Azure Monitor são separados em métricas leves e que podem dar suporte a logs e cenários quase em tempo real que são usados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: 6fc568546721511f6289600148919d28773058f4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002260"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Dados de monitoramento coletados pelo Azure Monitor
O [Azure Monitor](../overview.md) é um serviço que ajuda você a monitorar seus aplicativos e os recursos de que eles dependem. Essencial para essa função é o armazenamento de telemetria e outros dados de recursos monitorados. Este artigo fornece uma descrição completa de como esses dados são armazenados e usados pelo Azure Monitor.

Todos os dados coletados pelo Azure Monitor se enquadram em um dos dois tipos fundamentais, [métricas](#metrics) e [logs](#logs). Métricas são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Elas são leves e podem dar suporte a cenários quase em tempo real. Os logs contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os dados telemétricos, como eventos e rastreamentos, são armazenados como logs acrescidos dos dados de desempenho, de modo que possam todos ser combinados para análise.

![Visão geral do Azure Monitor](media/data-collection/overview.png)

## <a name="metrics"></a>Métricas
Métricas são valores numéricos que descrevem algum aspecto de um sistema em um momento específico. Elas são leves e podem dar suporte a cenários quase em tempo real. As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Elas são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples.

Por exemplo, você pode coletar a utilização do processador de uma máquina virtual a cada minuto ou o número de usuários conectados ao seu aplicativo a cada 10 minutos. Você pode acionar um alerta quando um desses valores coletados, ou até mesmo a diferença entre dois valores, excede um limite definido.

Os atributos específicos de métricas no Azure incluem o seguinte:

* Coletados com frequência de um minuto (exceto se especificado de outra forma na definição da métrica).
* Identificados exclusivamente por um nome de métrica e um namespace que atua como uma categoria.
* Armazenados por 93 dias. Você pode copiar métricas para logs para tendências de longo prazo.

Cada valor de métrica tem as seguintes propriedades:
* A hora em que o valor foi coletado.
* O tipo de medida que o valor representa.
* O recurso ao qual a rede virtual está associada.
* O valor em si.
* Algumas métricas podem ter várias dimensões, conforme descrito na próxima seção. As métricas personalizadas podem ter até 10 dimensões.

### <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Dimensões de uma métrica são pares nome-valor que contêm dados adicionais para descrever o valor da métrica. Por exemplo, uma métrica _Espaço em disco disponível_ pode ter uma dimensão chamada _Unidade_ com valores _C:_, _D:_, que permitiria a exibição do espaço em disco disponível em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados de uma métrica hipotética chamada _Taxa de Transferência de Rede_. O primeiro conjunto de dados não tem nenhuma dimensão. O segundo conjunto de dados mostra os valores com duas dimensões, _Endereço IP_ e _Direção_:

### <a name="network-throughput"></a>Taxa de Transferência de Rede

| Timestamp     | Valor da Métrica |
| ------------- |:-------------|
| 9/8/2017 8h14 | 1.331,8 Kbps |
| 9/8/2017 8h15 | 1.141,4 Kbps |
| 9/8/2017 8h16 | 1.110,2 Kbps |

Essa métrica não dimensional pode responder apenas a uma pergunta básica, como “qual era minha taxa de transferência de rede em determinado horário?”

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Taxa de Transferência de Rede + duas dimensões (“IP” e “Direção”)

| Timestamp     | Dimensão “IP”   | Dimensão “Direção” | Valor da Métrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Send"    | 646,5 Kbps |
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Receive" | 420,1 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Send"    | 515,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Receive" | 371,1 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 Kbps |

Essa métrica pode responder a perguntas como “qual era a taxa de transferência de rede para cada endereço IP?” e “quantos dados foram enviados vs. recebidos?” As métricas multidimensionais trazem um valor analítico e de diagnóstico adicional em comparação às métricas não dimensionais.

### <a name="value-of-metrics"></a>Valor de métricas
Métricas individuais normalmente fornecem pouco insight sozinhas. Elas fornecem um único valor sem nenhum contexto diferente da comparação com um limite simples. São valiosas quando combinadas com outras métricas para identificar padrões e tendências ou quando combinadas com logs que fornecem contexto sobre determinados valores.

Por exemplo, um determinado número de usuários em seu aplicativo em um determinado momento pode lhe dizer pouco sobre a integridade do aplicativo. Mas, uma queda repentina nos usuários indicados por vários valores da mesma métrica pode indicar um problema. Exceções excessivas geradas pelo aplicativo e indicadas por uma métrica separada podem identificar um problema do aplicativo que causa a queda. Eventos que o aplicativo cria para identificar falhas em seus componentes podem ajudá-lo a identificar a causa raiz.

### <a name="sources-of-metric-data"></a>Fontes de dados de métrica
Há três fontes fundamentais de métricas coletadas pelo Azure Monitor. Todas essas métricas estão disponíveis no repositório de métrica, onde elas podem ser avaliadas em conjunto, independentemente de sua origem.

As **métricas de plataforma** são criadas pelos recursos do Azure e esclarecem a integridade e o desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem a necessidade de configuração. 

As **métricas de aplicativo** são criadas pelo Application Insights para seus aplicativos monitorados e ajudam você a detectar problemas de desempenho e acompanhar as tendências de como o aplicativo está sendo usado. Isso inclui valores como _Tempo de resposta do servidor_ e _Exceções de navegador_.

As **métricas personalizadas** são métricas definidas além das métricas padrão disponibilizadas automaticamente. As métricas personalizadas devem ser criadas em relação a um único recurso na mesma região desse recurso. Você pode criar métricas personalizadas usando os seguintes métodos:
- [Definir métricas personalizadas no aplicativo](../../azure-monitor/app/api-custom-events-metrics.md) que está sendo monitorado pelo Application Insights. Elas acrescem ao conjunto padrão de métricas de aplicativo.
- Publicar métricas personalizadas de suas máquinas virtuais do Windows usando a [WAD (Extensão de Diagnóstico do Windows)](../../azure-monitor/platform/diagnostics-extension-overview.md).
- Publicar métricas personalizadas de suas máquinas virtuais do Linux usando o [agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).
- Gravar métricas personalizadas de um serviço do Azure usando as API de métricas personalizadas.

![Visão geral das métricas](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>O que você pode fazer com as métricas?
As tarefas que você pode executar com as métricas incluem o seguinte:

- Usar a [análise de métricas](metrics-charts.md) para analisar métricas coletadas e plotá-las em um gráfico. Controlar o desempenho de um recurso (como aplicativo lógico, site ou VM) fixando gráficos em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurar uma [regra de alerta da métrica](alerts-metric.md) que envia uma notificação ou realiza uma [ação automatizada](action-groups.md) quando a métrica ultrapassa o limite definido.
- Usar o [Dimensionamento automático](autoscale-overview.md) para aumentar ou diminuir os recursos com base em uma métrica que está ultrapassando um limite.
- Encaminhar as métricas para os logs para analisar dados de métrica com dados de log e armazenar os valores de métrica por mais de 93 dias. 
- Transmitir métricas para um [Hub de Eventos](stream-monitoring-data-event-hubs.md) para encaminhá-las ao [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) ou a sistemas externos.
- [Arquive](../../azure-monitor/learn/tutorial-archive-data.md) o histórico de desempenho ou integridade do recurso para fins de conformidade, auditoria ou geração de relatórios offline.
- Acessar valores de métrica em uma linha de comando ou aplicativo personalizado usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) ou a [API REST](rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Exibindo métricas
As métricas no Azure Monitor são armazenadas em um banco de dados de série temporal otimizado para recuperação rápida e armazena valores de métrica por 93 dias. Copie as métricas para os logs para análise e tendências de longo prazo.

Os dados de métrica são usados em uma variedade de formas, conforme descrito acima. Usar a [análise de métricas](metrics-charts.md) para analisar diretamente os dados no repositório de métrica e ver o gráfico de várias métricas ao longo do tempo. É possível exibir os gráficos interativamente ou fixá-los em um painel para exibi-los com outras visualizações. Também é possível recuperar métricas usando a [API REST de monitoramento do Azure](rest-api-walkthrough.md).

![Análise de Métricas](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>Logs

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Os logs contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os logs podem conter valores numéricos como métricas, mas normalmente contêm dados de texto com descrições detalhadas. Além disso, eles são diferentes das métricas, porque variam em sua estrutura e geralmente não são coletados em intervalos regulares.

Um tipo comum de entrada de log é um evento que é coletado esporadicamente. São criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer um contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

Logs são especialmente úteis para combinar dados de uma variedade de fontes a fim de obter análise complexa e tendências ao longo do tempo. Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. As métricas são até mesmo replicadas em logs para combiná-los com outros dados de monitoramento a fim de obter tendências e outras análises de dados.



### <a name="sources-of-log-data"></a>Fontes dos dados de log
O Azure Monitor pode coletar dados de log de várias origens no Azure e de recursos locais. Fontes de dados de log incluem o seguinte:

- [Logs de atividades](collect-activity-logs.md) de recursos do Azure, que incluem informações sobre suas configurações e integridade, e [Logs de diagnóstico](diagnostic-logs-stream-log-store.md), que fornecem insights sobre sua operação.
- Agentes em máquinas virtuais do [Windows](agent-windows.md) e do [Linux](../learn/quick-collect-linux-computer.md) que enviam telemetria do sistema operacional convidado e dos aplicativos para o Azure Monitor de acordo com as [Fontes de Dados](data-sources.md) que você configurar.
- Dados do aplicativo coletados pelo [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Dados que fornecem informações sobre determinado aplicativo ou serviço das [soluções de monitoramento](../insights/solutions.md) ou recursos como Container Insights, Insights de VM ou Insights do Grupo de Recursos.
- Dados de segurança coletados pela [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/).
- [Métricas](#metrics) de recursos do Azure. Isso permite que você armazene métricas por mais de 93 dias e as analise com outros dados de log.
- Telemetria gravada no [Armazenamento do Azure](azure-storage-iis-table.md).
- Dados personalizados de um cliente de API REST usando o cliente [API do Coletor de Dados de HTTP](data-collector-api.md) ou de um fluxo de trabalho do [Aplicativo Lógico do Azure](https://docs.microsoft.com/azure/logic-apps/).

![Visão geral de logs](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>O que você pode fazer com os logs?
As tarefas que você pode executar com os logs incluem o seguinte:

- Use o [Log Analytics](../log-query/get-started-portal.md) no portal do Azure para gravar consultas que analisam dados de log. Fixar os resultados renderizados como tabelas ou gráficos em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md).
- Configurar uma [regra de alerta de log](alerts-log.md) que envia uma notificação ou realiza [ação automatizada](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.
- Compile um fluxo de trabalho baseado em dados de log usando [Aplicativos Lógicos](~/articles/logic-apps/index.yml).
- Exportar os resultados de uma consulta para o [Power BI](powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.
- Acessar valores de métrica em uma linha de comando ou aplicativo personalizado usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) ou a [API REST](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Exibindo dados de log
Todos os dados de log no Azure Monitor são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) gravada com a [linguagem de consulta do Data Explorer](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados coletados. Use o [Log Analytics](../log-query/portals.md) para gravar e testar consultas no portal do Azure. É possível trabalhar com os resultados de forma interativa ou fixá-los em um painel para exibi-los com outras visualizações. Também é possível recuperar logs, usando a [API REST de monitoramento do Azure](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

> [!IMPORTANT]
> Os dados do Application Insights são armazenados em uma partição separada de outros dados de log no Azure Monitor. Isso dá suporte à mesma funcionalidade que outros dados de log, porém é necessário usar o [console do Application Insights](../app/analytics.md) ou a [API do Application Insights](https://dev.applicationinsights.io/) para acessar esses dados. Você pode usar uma [consulta entre recursos](../log-query/cross-workspace-query.md) para analisar os dados do aplicativo junto com outros dados de log.

![Logs](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Converter dados de monitoramento

### <a name="metrics-to-logs"></a>Métricas para logs
É possível copiar as métricas nos logs para executar análises complexas com outros tipos de dados usando a linguagem de consulta avançada do Azure Monitor. Também é possível reter dados de log para períodos mais longos do que métricas, o que permite realizar as tendências com o tempo. Use métricas para dar suporte a análise quase e tempo real e a alertas ao usar logs para obter tendências e análise com outros dados.

É possível obter orientações sobre como coletar métricas dos recursos do Azure em [Coletar métricas e logs de serviço do Azure para uso no Azure Monitor](collect-azure-metrics-logs.md). Obtenha orientações sobre como coletar métricas de recursos dos recursos de PaaS do Azure em [Configurar coleção de métricas de recurso de PaaS do Azure com Azure Monitor](collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Logs para métricas
Conforme descrito acima, as métricas são mais dinâmicas do que logs, o que permite que você crie alertas com latência menor e a um custo mais baixo. Uma quantidade significativa de dados numéricos é armazenada como logs que seriam adequados para métricas, mas não são armazenados como métricas no Azure Monitor. Um exemplo comum são os dados de desempenho coletados de agentes e de soluções de gerenciamento. Alguns desses valores podem ser copiados em métricas, onde estão disponíveis para alertas e para análise com o Metrics Explorer.

A explicação desse recurso está disponível em [Criar alertas de métrica para logs no Azure Monitor](alerts-metric-logs.md). A lista de valores compatíveis está disponível em [Métricas compatíveis com o Azure Monitor](metrics-supported.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Transmitir dados para sistemas externos
Além de usar as ferramentas no Azure para analisar os dados de monitoramento, pode haver a exigência de encaminhá-los para uma ferramenta externa, como um produto SIEM (gerenciamento de eventos e informações de segurança). Esse encaminhamento normalmente é feito diretamente de recursos monitorados por meio dos [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/).

É possível obter orientação para os diferentes tipos de dados de monitoramento em [Stream Azure monitoring data to an event hub for consumption by an external tool](stream-monitoring-data-event-hubs.md) (Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
