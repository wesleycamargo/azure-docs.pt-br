---
title: Fontes de dados no Azure Monitor | Microsoft Docs
description: Descreve os dados disponíveis para monitorar a integridade e desempenho de seus recursos do Azure e dos aplicativos executados neles.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: b10236a1e0307c9464d58e50eb0c7b4e6a60b5e5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987742"
---
# <a name="sources-of-data-in-azure-monitor"></a>Fontes de monitoramento no Azure Monitor
Este artigo descreve as fontes de dados coletadas pelo Azure Monitor para monitorar a integridade e o desempenho de seus recursos e dos aplicativos executados nelas. Esses recursos podem ser no Azure, em outra nuvem ou localmente.  Veja os [Dados coletados pelo Azure Monitor](monitoring-data-collection.md) para obter detalhes sobre como esses dados são armazenados e como você pode exibi-los.

Os dados de monitoramento no Azure vêm de uma variedade de fontes que podem ser organizadas em camadas, sendo que a camada mais alta é o seu aplicativo e quaisquer sistemas operacionais e a camada mais baixa é a plataforma do Azure. Isso é ilustrado no diagrama a seguir com cada camada descrita detalhadamente nas seções a seguir.

![Tipos de dados de monitoramento](media/monitoring-data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Locatário do Azure
A telemetria relacionada ao seu locatário do Azure é coletada de serviços de todos os locatários como o Azure Active Directory.

![Coleção de locatário do Azure](media/monitoring-data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Log de Auditoria do Azure Active Directory
Os [relatórios do Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) contêm o histórico de atividade de entrada e a trilha de auditoria das alterações feitas em um locatário específico. Esses logs de auditoria podem ser gravados no Log Analytics para analisá-los com outros dados de log.


## <a name="azure-platform"></a>Plataforma do Azure
A telemetria relacionada à integridade e à operação do Azure propriamente dito inclui dados sobre a operação e o gerenciamento da sua assinatura do Azure. Ele inclui dados de integridade de serviço armazenados no Log de Atividades do Azure e em logs de auditoria do Azure Active Directory.

![Coleção de assinatura do Azure](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Integridade do Serviço do Azure
[A Integridade do Serviço do Azure](../monitoring-and-diagnostics/monitoring-service-notifications.md) fornece informações sobre a integridade dos serviços do Azure na assinatura dos quais o aplicativo e os recursos dependem. Você pode criar alertas para ser notificado sobre problemas críticos atuais e esperados que podem afetar o aplicativo. Registros da Integridade do Serviço são armazenados no [Log de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), portanto, você pode exibi-los no Explorador do Log de Atividades e copiá-los para o Log Analytics.

### <a name="azure-activity-log"></a>Log de Atividades do Azure
O [Log de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) inclui registros de integridade do serviço junto com os registros de alterações de configuração feitas aos recursos do Azure. O Log de Atividades está disponível para todos os recursos do Azure e representa a exibição _externa_ desses recursos. Os tipos específicos de registros no Log de Atividades são descritos no [esquema de eventos do Log de Atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Você pode exibir o Log de Atividades para um recurso específico na página desse recurso no portal do Azure ou exibir logs de vários recursos no [Explorador do Log de Atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). É particularmente útil copiar as entradas de log para o Log Analytics para combiná-las com outros dados de monitoramento. Você também pode enviá-las para outros locais usando [Hubs de Eventos](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Serviços do Azure
Métricas e logs de diagnóstico em nível de recurso fornecem informações sobre a operação _interna_ dos recursos do Azure. Eles estão disponíveis para a maioria dos serviços Azure, sendo que as soluções de gerenciamento fornecem informações adicionais sobre serviços específicos.

![Coleção de recursos do Azure](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Métricas
A maioria dos serviços do Azure gerará [métricas de plataforma](monitoring-data-collection.md#metrics) que refletirão seu desempenho e operação. As [métricas específicas variam para cada tipo de recurso](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Elas são acessíveis no Metrics Explorer e podem ser copiadas para o Log Analytics para análise de tendências e outras análises.


### <a name="resource-diagnostic-logs"></a>Logs de diagnóstico de recurso
Embora o Log de Atividades forneça informações sobre as operações executadas em recursos do Azure, [logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) em nível de recurso fornecem informações sobre a operação do recurso propriamente dito.   Os requisitos de configuração e o conteúdo desses logs [variam de acordo com o tipo de recurso](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Você não pode exibir os logs de diagnóstico diretamente no portal do Azure, mas você pode [enviá-los para o Armazenamento do Azure para arquivamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) e exportá-los para [Hub de Eventos](../event-hubs/event-hubs-what-is-event-hubs.md) para redirecionamento a outros serviços, ou então para o [Log Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) para análise. Alguns recursos podem gravar diretamente no Log Analytics, enquanto outros gravam em uma conta de armazenamento antes de serem [importados para o Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Soluções de monitoramento
 As [soluções de monitoramento](../monitoring/monitoring-solutions.md) coletam dados para fornecer insight adicional sobre a operação de um serviço ou aplicativo específico. Elas coletam dados para o Log Analytics, em que elas podem ser analisadas usando a [linguagem de consulta](../log-analytics/log-analytics-log-search.md) ou [exibições](../log-analytics/log-analytics-view-designer.md) que são normalmente incluídas na solução.

## <a name="guest-operating-system"></a>Sistema operacional convidado
Recursos de computação no Azure, em outras nuvens e localmente têm um sistema operacional convidado para monitorar. Com a instalação de um ou mais agentes, você pode coletar a telemetria do convidado nas mesmas ferramentas de monitoramento que os serviços do Azure propriamente ditos.

![Coleta de recursos de computação do Azure](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Extensão de Diagnóstico
Com a [Extensão de Diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md), você pode coletar logs e dados de desempenho do sistema operacional cliente de recursos de computação do Azure. Métricas e logs coletados de clientes são armazenados em uma conta de armazenamento do Azure, da qual eles [podem ser importados pelo Log Analytics se você o configurar para isso](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  O Metrics Explorer compreende como ler da conta de armazenamento e incluirá métricas de cliente com outras métricas coletadas.


### <a name="log-analytics-agent"></a>Agente do Log Analytics
Você pode instalar o agente do Log Analytics em qualquer máquina virtual do [Windows](../log-analytics/log-analytics-agent-windows.md) ou [Linux]() ou no computador físico. A máquina virtual pode estar em execução no Azure, em outra nuvem ou localmente.  O agente se conecta ao Log Analytics diretamente ou então por meio de um [grupo de gerenciamento do System Center Operations Manager conectado](../log-analytics/log-analytics-om-agents.md), e permite que você colete dados das [fontes de dados](../log-analytics/log-analytics-data-sources.md) que você configura ou de [soluções de gerenciamento](../monitoring/monitoring-solutions.md) que fornecem informações adicionais para os aplicativos em execução na máquina virtual.

### <a name="service-map"></a>Mapa do Serviço
O [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) requer um Dependency Agent em máquinas virtuais do Windows e do Linux. Isso funciona com o agente do Log Analytics para coletar dados sobre processos em execução na máquina virtual e dependências em processos externos. Ele armazena esses dados no Log Analytics e inclui um console que exibe visualmente os dados coletados, além de outros dados armazenados no Log Analytics.

## <a name="applications"></a>APLICATIVOS
Além da telemetria que seu aplicativo pode gravar no sistema operacional convidado, o monitoramento detalhado do aplicativo de é realizado com o [Application Insights](https://docs.microsoft.com/azure/application-insights/). O Application Insights pode coletar dados de aplicativos em execução em uma variedade de plataformas. O aplicativo pode estar em execução no Azure, em outra nuvem ou localmente.

![Coleta de dados do aplicativo](media/monitoring-data-sources/application-collection.png)


### <a name="application-data"></a>Dados do aplicativo
Quando você habilita o Application Insights para um aplicativo por meio da instalação de um pacote de instrumentação, ele coleta as métricas e logs relacionados ao desempenho e à operação do aplicativo. Isso inclui informações detalhadas sobre exibições de página, solicitações de aplicativo e exceções. O Application Insights armazena os dados coletados nas métricas do Azure e no Log Analytics. Ele inclui ferramentas abrangentes para analisar esses dados, mas você também pode analisá-los com dados de outras fontes usando ferramentas como o Metrics Explorer e pesquisas de log.

Você também pode usar o Application Insights para [criar uma métrica personalizada](../application-insights/app-insights-api-custom-events-metrics.md).  Isso permite que você defina sua própria lógica para calcular um valor numérico e, em seguida, armazenar esse valor com outras métricas que podem ser acessadas a partir do Metrics Explorer e usadas para [autoescala](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) e alertas de métrica.

### <a name="dependencies"></a>Dependências
Para monitorar as diferentes operações lógicas de um aplicativo, você precisa [coletar a telemetria em vários componentes](../application-insights/app-insights-transaction-diagnostics.md). O Application Insights dá suporte à [correlação de telemetria distribuída](../application-insights/application-insights-correlation.md), que identifica as dependências entre componentes, permitindo que você os analise conjuntamente.

### <a name="availability-tests"></a>Testes de disponibilidade
Os [testes de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md) no Application Insights permitem que você teste a disponibilidade e a capacidade de resposta de seu aplicativo de diferentes locais na Internet pública. Você pode fazer um teste de ping simples para verificar se o aplicativo está ativo ou usar o Visual Studio para criar um teste da Web que simula um cenário de usuário.  Testes de disponibilidade não exigem nenhuma instrumentação no aplicativo.

## <a name="custom-sources"></a>Fontes personalizadas
Além das camadas padrão de um aplicativo, você precisa monitorar outros recursos que têm a telemetria que não pode ser coletada com outras fontes de dados. Para esses recursos, você precisará gravar esses dados usando uma API do Azure Monitor.

![Coleção de dados personalizada](media/monitoring-data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API do Coletor de Dados
O Azure Monitor pode coletar dados de log de qualquer cliente REST usando a [API do coletor de dados](../log-analytics/log-analytics-data-collector-api.md). Isso permite que você crie cenários de monitoramento personalizados e estenda o monitoramento para recursos que não expõem a telemetria por meio de outras fontes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [tipos de dados de monitoramento coletados pelo Azure Monitor](monitoring-data-collection.md) e como exibir e analisar esses dados.
