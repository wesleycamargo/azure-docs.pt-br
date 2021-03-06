---
title: O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos
description: Visão geral da desativação de serviços e funcionalidade de monitoramento clássicos, mostrada anteriormente no portal do Azure em Alertas (clássico). O alerta e monitoramento clássico inclui alertas métricos clássicos para recursos do Azure, alertas de métricas clássicas para Application Insights, alertas de webtest clássicos para Application Insights, alertas baseados em métricas personalizadas clássicas para Application Insights e alertas clássicos para o SmartDetection v1 do Application Insights
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 7cca9d9c91f35c30bbd71ae82b9f02447051078d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983161"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

O Azure Monitor se tornou um serviço unificado de monitoramento de pilha completa, que agora dá suporte a "Uma métrica" e "Um alerta" nos recursos. Para obter mais informações, confira nossa [postagem sobre o novo Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram desenvolvidas para serem mais rápidas, mais inteligentes e extensíveis – acompanhando a crescente expansão da computação em nuvem e em linha com a filosofia de Nuvem Inteligente da Microsoft. 

Com a nova plataforma de monitoramento e alerta do Azure em vigor, desativaremos a plataforma de monitoramento e alerta "clássica". Hospedada na seção *exibir alertas clássicos* dos alertas do Azure, ela **será preterida até junho de 2019 nas nuvens públicas do Azure**. A [nuvem do Azure Governamental](../../azure-government/documentation-government-welcome.md) não será afetada.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma. Para os clientes que têm um grande número de alertas, estamos trabalhando para fornecer uma maneira automatizada de mover os alertas clássicos existentes para o novo sistema de alertas sem interrupções ou custos adicionais.

> [!IMPORTANT]
> Regras de alerta clássicas criadas no Log de Atividades não serão preteridas nem migradas. Todas as regras de alerta clássicas criadas no Log de Atividades podem ser acessadas e usadas no estado em que se encontram nos novos Alertas do Azure Monitor. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de log de atividades usando o Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). De forma semelhante, os Alertas na Integridade do Serviço podem ser acessados e usados no estado em que se encontram na nova seção de Integridade do Serviço. Para obter detalhes, confira [alertas em notificações da Integridade do Serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicativos

Agora, a plataforma de métrica mais recente do Monitor do Azure agora ativará o monitoramento proveniente do Application Insights. Essa movimentação significa que o Application Insights se conectará a Action Groups, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem acionar chamadas de voz, funções do Azure, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e os Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que os usuários do Application Insights aproveitem a mesma tecnologia que potencializa o monitoramento em outros recursos do Azure e sustenta o monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para Application Insights abrangerá:

- **Métricas do Application Insights plataforma** – que fornece métricas predefinidas populares do produto do Application Insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights Disponibilidade e teste na Web** - que fornece a capacidade de avaliar a capacidade de resposta e a disponibilidade de seu aplicativo ou servidor da web. Para obter mais informações, consulte este artigo sobre o uso de [Testes de disponibilidade e alertas para o Application Insights no novo Monitor do Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Métricas do aplicativo Insights personalizado** – que permite definir e emitem suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar [métrica personalizada para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente)** - que notifica automaticamente em tempo real se o seu aplicativo da Web sofrer um aumento anormal na taxa de solicitações HTTP com falha ou chamadas de dependência. Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente) como parte do novo Monitor do Azure, estarão disponíveis em breve e atualizaremos este documento com links na próxima iteração à medida que for sendo implementado nos próximos meses.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas e Alertas unificadas para outros recursos do Azure

Desde março de 2018, a próxima geração de monitoramento multidimensional e de alertas para recursos do Azure está em disponibilidade. Agora, a nova plataforma métrica e os alertas são mais rápidos com recursos quase em tempo real. Mais importante ainda, os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais nova inclui a opção de dimensões, que permitem dividir e filtrar para combinação, condição ou operação de valor específico. Como todos os alertas no novo Monitor do Azure, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups - permitindo que as notificações se expandam além do e-mail ou webhook para SMS, voz, função do Azure, Runbook de automação e muito mais. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
As métricas mais recentes para recursos do Azure estão disponíveis como:

- **Monitor do Azure Métricas de plataforma padrão** - que fornece métricas populares pré-preenchidas de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte no Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e [dão suporte a alertas de métrica no Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas do Monitor do Azure** - que fornece métricas de fontes controladas pelo usuário, incluindo o agente do Azure Diagnostics. Para obter mais informações, consulte este artigo sobre [métricas personalizadas no Monitor do Azure](../../azure-monitor/platform/metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar as métricas coletadas pelo [agente do Windows Azure Diagnostics](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e [InfluxData Telegraf agente](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação do clássico, monitoramento e alertas de plataforma

Conforme mencionado anteriormente, clássico de monitoramento e alerta plataforma atualmente utilizável na [seção de alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do Azure portal será desativado em provenientes dado meses eles foram substituídos por sistema mais recente.
Monitorizações e alertas clássicos mais antigos serão retirados em 30 de junho de 2019; incluindo o encerramento de APIs relacionadas, interface do portal do Azure e Serviços. Especificamente, esses recursos serão preteridos:

- Métricas e alertas mais antigos (clássicos) para recursos do Azure como disponíveis atualmente por meio da seção [Alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure; acessível como recurso de [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Plataformas e métricas personalizadas (clássicas) mais antigas para o Application Insights, bem como alertas sobre elas, como estão atualmente disponíveis na seção [Alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure e acessível como recurso de [microsoft.insights / alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Mais antiga alerta anomalias de falha (clássica) está disponível como [detecção inteligente no Application Insights](../../azure-monitor/app/proactive-diagnostics.md) no portal do Azure; com alertas configurados mostrado na [alertas (clássica) seção](../../azure-monitor/platform/alerts-classic.overview.md) do Azure Portal

Todos os sistemas clássicos de monitoramento e alerta, incluindo a [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), o [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), a [CLI](../../azure-monitor/platform/alerts-classic-portal.md), a [página do portal do Azure](../../azure-monitor/platform/alerts-classic-portal.md) e o [Modelo de recurso](../../azure-monitor/platform/alerts-enable-template.md) correspondentes permanecerão utilizáveis até junho de 2019. 

No final de junho de 2019, no Azure Monitor:

- o serviço clássico de monitoramento e alertas serão desativados e deixarão de ficar disponíveis para a criação de novas regras de alerta
- As regras de alerta que continuarem existindo nos Alertas (clássico) depois de junho de 2019 continuar executando e acionando notificações, mas não estarão disponíveis para modificação.
- A partir de julho de 2019, as regras de alerta no serviço clássico de monitoramento e alertas serão migrados automaticamente pela Microsoft para seu equivalente na nova plataforma do Azure Monitor. O processo será contínuo sem nenhum tempo de inatividade e os clientes não terão nenhuma perda na cobertura de monitoramento.
- Regras de alerta migradas para a nova plataforma fornecerão cobertura de monitoramento como antes, mas dispararão notificação com novos conteúdos. Qualquer endereço de email, ponto de extremidade do webhook ou link de aplicativo lógico associado à regra de alerta clássica será transferido quando for migrado, mas poderá não se comportar corretamente, uma vez que o conteúdo do alerta será diferente na nova plataforma

> [!IMPORTANT]
> O Microsoft Azure Monitor fornecerá ferramentas para os clientes migrarem automaticamente suas regras de alerta clássicas para a nova plataforma em breve. E a executará de maneira forçada para todas as regras de alerta clássicas que ainda existem, começando em julho de 2019. Os clientes precisam garantir que a automação que consome conteúdo de regra de alerta clássica seja adaptada para lidar com o novo conteúdo de [Métricas e Alertas Unificados no Application Insights](#unified-metrics-and-alerts-in-application-insights) ou [Métricas e Alertas Unificados para outros recursos do Azure](#unified-metrics-and-alerts-for-other-azure-resources) após a migração das regras de alerta clássicas. 

Em breve, forneceremos ferramentas para permitir que você migre voluntariamente seus alertas da [seção Alertas (clássica)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure para os novos alertas do Azure. Todas as regras configuradas em Alertas (clássicos) que são migradas para o novo Monitor do Azure permanecerão gratuitas e não serão cobradas. As regras de alerta clássicas migradas também não suportarão qualquer cobrança por notificações push por e-mail, webhook ou LogicApp. No entanto, o uso de novos tipos de notificação ou ação (como SMS, Chamada de voz, integração de ITSM, etc.) será cobrado, se adicionado a um alerta novo ou migrado. Para obter mais informações, consulte [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Além disso, a seguir estarão sob o ambit de passíveis de cobrança [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure

Este artigo será links serão atualizadas continuamente e detalhes sobre o monitoramento do Azure novo & alerta de funcionalidade, bem como a disponibilidade de ferramentas para ajudar os usuários a adotar a nova plataforma do Azure Monitor.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../../azure-monitor/overview.md).
* Saiba mais sobre os novos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).

