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
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 5896083c9159c30e6b66009f43073391eec936ed
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003685"
---
# <a name="sources-of-data-in-azure-monitor"></a>Fontes de monitoramento no Azure Monitor
Este artigo descreve as fontes de dados coletadas pelo Azure Monitor para monitorar a integridade e o desempenho de seus recursos e dos aplicativos executados nelas. Esses recursos podem ser no Azure, em outra nuvem ou localmente.  Veja os [Dados coletados pelo Azure Monitor](data-collection.md) para obter detalhes sobre como esses dados são armazenados e como você pode exibi-los.

Os dados de monitoramento no Azure vêm de uma variedade de fontes que podem ser organizadas em camadas, sendo que a camada mais alta é o seu aplicativo e quaisquer sistemas operacionais e a camada mais baixa é a plataforma do Azure. Isso é ilustrado no diagrama a seguir com cada camada descrita detalhadamente nas seções a seguir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![Tipos de dados de monitoramento](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Locatário do Azure
A telemetria relacionada ao seu locatário do Azure é coletada de serviços de todos os locatários como o Azure Active Directory.

![Coleção de locatário do Azure](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Log de Auditoria do Azure Active Directory
Os [relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contêm o histórico de atividade de entrada e a trilha de auditoria das alterações feitas em um locatário específico. Esses logs de auditoria podem ser gravados nos logs do Azure Monitor para analisá-los com outros dados de log.


## <a name="azure-platform"></a>Plataforma do Azure
A telemetria relacionada à integridade e à operação do Azure propriamente dito inclui dados sobre a operação e o gerenciamento da sua assinatura do Azure. Ele inclui dados de integridade de serviço armazenados no Log de Atividades do Azure e em logs de auditoria do Azure Active Directory.

![Coleção de assinatura do Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Integridade do Serviço do Azure
[A Integridade do Serviço do Azure](service-notifications.md) fornece informações sobre a integridade dos serviços do Azure na assinatura dos quais o aplicativo e os recursos dependem. Você pode criar alertas para ser notificado sobre problemas críticos atuais e esperados que podem afetar o aplicativo. Registros da Integridade do Serviço são armazenados no [log de Atividades do Azure](activity-logs-overview.md), portanto, você pode exibi-los no Explorador do Log de Atividades e copiá-los para os logs do Azure Monitor.

### <a name="azure-activity-log"></a>Log de Atividades do Azure
O [Log de Atividades do Azure](activity-logs-overview.md) inclui registros de integridade do serviço junto com os registros de alterações de configuração feitas aos recursos do Azure. O Log de Atividades está disponível para todos os recursos do Azure e representa a exibição _externa_ desses recursos. Os tipos específicos de registros no Log de Atividades são descritos no [esquema de eventos do Log de Atividades do Azure](activity-log-schema.md).

Você pode exibir o Log de Atividades para um recurso específico na página desse recurso no portal do Azure ou exibir logs de vários recursos no [Explorador do Log de Atividades](activity-logs-overview.md). É particularmente útil copiar as entradas de log para o Azure Monitor para combiná-las com outros dados de monitoramento. Você também pode enviá-las para outros locais usando [Hubs de Eventos](activity-logs-stream-event-hubs.md).



## <a name="azure-services"></a>Serviços do Azure
Métricas e logs de diagnóstico em nível de recurso fornecem informações sobre a operação _interna_ dos recursos do Azure. Eles estão disponíveis para a maioria dos serviços Azure, sendo que as soluções de gerenciamento fornecem informações adicionais sobre serviços específicos.

![Coleção de recursos do Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Métricas
A maioria dos serviços do Azure gerará [métricas de plataforma](data-collection.md#metrics) que refletirão seu desempenho e operação. As [métricas específicas variam para cada tipo de recurso](metrics-supported.md).  Elas são acessíveis no Metrics Analytics e podem ser copiadas para os logs para análise de tendências e outras análises usando o Log Analytics.


### <a name="resource-diagnostic-logs"></a>Logs de diagnóstico de recurso
Embora o Log de Atividades forneça informações sobre as operações executadas em recursos do Azure, [logs de diagnóstico](diagnostic-logs-overview.md) em nível de recurso fornecem informações sobre a operação do recurso propriamente dito.   Os requisitos de configuração e o conteúdo desses logs [variam de acordo com o tipo de recurso](diagnostic-logs-schema.md).

Você não pode exibir os logs de diagnóstico diretamente no portal do Azure, mas você pode [enviá-los para o Armazenamento do Azure para arquivamento](archive-diagnostic-logs.md) e exportá-los para o [Hub de Eventos](../../event-hubs/event-hubs-about.md) para redirecionamento a outros serviços, ou então para o [Azure Monitor](diagnostic-logs-stream-log-store.md) para análise. Alguns recursos podem gravar diretamente no Azure Monitor, enquanto outros gravam em uma conta de armazenamento antes de serem [importados para o Log Analytics](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Soluções de monitoramento
 As [soluções de monitoramento](../../azure-monitor/insights/solutions.md) coletam dados para fornecer insight adicional sobre a operação de um serviço ou aplicativo específico. Elas coletam dados para os logs do Azure Monitor, em que podem ser analisadas usando a [linguagem de consulta](../../azure-monitor/log-query/log-query-overview.md) ou [exibições](view-designer.md) que são normalmente incluídas na solução.


## <a name="guest-operating-system"></a>Sistema operacional convidado
Recursos de computação no Azure, em outras nuvens e localmente têm um sistema operacional convidado para monitorar. Com a instalação de um ou mais agentes, você pode coletar a telemetria do convidado nas mesmas ferramentas de monitoramento que os serviços do Azure propriamente ditos.

![Coleta de recursos de computação do Azure](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Com a extensão de Diagnóstico do Azure, ele fornece um nível básico de monitoramento, coletando logs e dados de desempenho do sistema operacional do cliente dos recursos de computação do Azure.   

### <a name="log-analytics-agent"></a>Agente do log Analytics
O monitoramento e o gerenciamento abrangentes de suas máquinas virtuais Windows ou Linux ou do computador físico são fornecidos com o agente do Log Analytics. A máquina virtual pode estar em execução no Azure, em outra nuvem ou no local e o agente se conecta ao Azure Monitor diretamente ou por meio do System Center Operations Manager e permite coletar dados de [fontes de dados](agent-data-sources.md) configuradas ou de [soluções de monitoramento](../../azure-monitor/insights/solutions.md) que fornecem informações adicionais sobre aplicativos executados na máquina virtual.


### <a name="dependency-agent"></a>Agente de dependência
O [Mapa de Serviço](../insights/service-map.md) e o [ Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md) exigem um Agente de Dependência em máquinas virtuais Windows e Linux. Isso se integra ao agente do Log Analytics para coletar dados descobertos sobre processos em execução na máquina virtual e dependências externas do processo. Ele armazena esses dados no Azure Monitor e visualiza os componentes interconectados descobertos.  

Para entender melhor as diferenças entre os agentes e quais usar, dependendo de seus requisitos de monitoramento, consulte a [visão geral dos agentes de monitoramento](agents-overview.md).

## <a name="applications"></a>APLICATIVOS
Além da telemetria que seu aplicativo pode gravar no sistema operacional convidado, o monitoramento detalhado do aplicativo de é realizado com o [Application Insights](https://docs.microsoft.com/azure/application-insights/). O Application Insights pode coletar dados de aplicativos em execução em uma variedade de plataformas. O aplicativo pode estar em execução no Azure, em outra nuvem ou localmente.

![Coleta de dados do aplicativo](media/data-sources/application-collection.png)


### <a name="application-data"></a>Dados do aplicativo
Quando você habilita o Application Insights para um aplicativo por meio da instalação de um pacote de instrumentação, ele coleta as métricas e logs relacionados ao desempenho e à operação do aplicativo. Isso inclui informações detalhadas sobre exibições de página, solicitações de aplicativo e exceções. O Application Insights armazena os dados que coleta no Azure Monitor. Ele inclui ferramentas abrangentes para analisar esses dados, mas você também pode analisá-los com dados de outras fontes usando ferramentas como a análise de métrica e a análise de logs.

Você também pode usar o Application Insights para [criar uma métrica personalizada](../../application-insights/app-insights-api-custom-events-metrics.md).  Isso permite que você defina sua própria lógica para calcular um valor numérico e, em seguida, armazenar esse valor com outras métricas que podem ser acessadas a partir do Metrics Analytics e usadas para [Dimensionamento automático](autoscale-custom-metric.md) e alertas de métrica.


### <a name="dependencies"></a>Dependências
Para monitorar as diferentes operações lógicas de um aplicativo, você precisa [coletar a telemetria em vários componentes](../../application-insights/app-insights-transaction-diagnostics.md). O Application Insights dá suporte à [correlação de telemetria distribuída](../../application-insights/application-insights-correlation.md), que identifica as dependências entre componentes, permitindo que você os analise conjuntamente.

### <a name="availability-tests"></a>Testes de disponibilidade
Os [testes de disponibilidade](../../application-insights/app-insights-monitor-web-app-availability.md) no Application Insights permitem que você teste a disponibilidade e a capacidade de resposta de seu aplicativo de diferentes locais na Internet pública. Você pode fazer um teste de ping simples para verificar se o aplicativo está ativo ou usar o Visual Studio para criar um teste da Web que simula um cenário de usuário.  Testes de disponibilidade não exigem nenhuma instrumentação no aplicativo.

## <a name="custom-sources"></a>Fontes personalizadas
Além das camadas padrão de um aplicativo, você precisa monitorar outros recursos que têm a telemetria que não pode ser coletada com outras fontes de dados. Para esses recursos, você precisará gravar esses dados usando uma API do Azure Monitor.

![Coleção de dados personalizada](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API do Coletor de Dados
O Azure Monitor pode coletar dados de log de qualquer cliente REST usando a [API do Coletor de Dados](../../azure-monitor/platform/data-collector-api.md). Isso permite que você crie cenários de monitoramento personalizados e estenda o monitoramento a recursos que não expõem a telemetria por outras fontes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [tipos de dados de monitoramento coletados pelo Azure Monitor](data-collection.md) e como exibir e analisar esses dados.
