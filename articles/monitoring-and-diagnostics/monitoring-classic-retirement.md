---
title: O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos
description: Visão geral da desativação de serviços e funcionalidade de monitoramento clássicos, mostrada anteriormente no portal do Azure em Alertas (clássico). O alerta e monitoramento clássico inclui alertas métricos clássicos para recursos do Azure, alertas de métricas clássicas para Application Insights, alertas de webtest clássicos para Application Insights, alertas baseados em métricas personalizadas clássicas para Application Insights e alertas clássicos para o SmartDetection v1 do Application Insights
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f7efafe5e3080de15781496032b688bc5fa71df2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418408"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

O Monitor do Azure agora se tornou um serviço de monitoramento de pilha completa unificado que agora suporta "One Metrics" e "One Alerts" entre os recursos; Para obter mais informações, consulte nossa [postagem no novo monitor do Azure](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram desenvolvidas para serem mais rápidas, mais inteligentes e extensíveis - acompanhando a crescente expansão da computação em nuvem e linha com a filosofia Microsoft Intelligent Cloud. 

Com a nova plataforma de monitoramento e alerta do Azure em vigor, aposentaremos a plataforma de monitoramento e alerta "clássica" - hospedada na seção *visualizar alertas clássicos* dos alertas do Azure, será suspenso até junho de 2019.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma. Para os clientes que têm um grande número de alertas, estamos trabalhando para fornecer uma maneira automatizada de mover os alertas clássicos existentes para o novo sistema de alertas sem interrupções ou custos adicionais.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicativos

Agora, a plataforma de métrica mais recente do Monitor do Azure agora ativará o monitoramento proveniente do Application Insights. Essa movimentação significa que o Application Insights se conectará a Action Groups, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem acionar chamadas de voz, funções do Azure, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e os Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que os usuários do Application Insights aproveitem a mesma tecnologia que potencializa o monitoramento em outros recursos do Azure e sustenta o monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para Application Insights abrangerá:

- **Métricas do Application Insights plataforma** – que fornece métricas predefinidas populares do produto do Application Insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para o Application Insights no novo Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights Disponibilidade e teste na Web** - que fornece a capacidade de avaliar a capacidade de resposta e a disponibilidade de seu aplicativo ou servidor da web. Para obter mais informações, consulte este artigo sobre o uso de [Testes de disponibilidade e alertas para o Application Insights no novo Monitor do Azure](../application-insights/app-insights-monitor-web-app-availability.md).
- **Métricas do aplicativo Insights personalizado** – que permite definir e emitem suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar [métrica personalizada para o Application Insights no novo Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente)** - que notifica automaticamente em tempo real se o seu aplicativo da Web sofrer um aumento anormal na taxa de solicitações HTTP com falha ou chamadas de dependência. Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente) como parte do novo Monitor do Azure, estarão disponíveis em breve e atualizaremos este documento com links na próxima iteração à medida que for sendo implementado nos próximos meses.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Unificada métricas e alertas para outros recursos do Azure

Desde março de 2018, a próxima geração de monitoramento multidimensional e de alertas para recursos do Azure está em disponibilidade. Agora, a nova plataforma métrica e os alertas são mais rápidos com recursos quase em tempo real. Mais importante ainda, os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais nova inclui a opção de dimensões, que permitem dividir e filtrar para combinação, condição ou operação de valor específico. Como todos os alertas no novo Monitor do Azure, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups - permitindo que as notificações se expandam além do e-mail ou webhook para SMS, voz, função do Azure, Runbook de automação e muito mais.
As métricas mais recentes para recursos do Azure estão disponíveis como:

- **Monitor do Azure Métricas de plataforma padrão** - que fornece métricas populares pré-preenchidas de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte no Azure Monitor](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) e [dão suporte a alertas de métrica no Azure Monitor](alert-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas do Monitor do Azure** - que fornece métricas de fontes controladas pelo usuário, incluindo o agente do Azure Diagnostics. Para obter mais informações, consulte este artigo sobre [métricas personalizadas no Monitor do Azure](metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar as métricas coletadas pelo [agente do Windows Azure Diagnostics](metrics-store-custom-guestos-resource-manager-vm.md) e [InfluxData Telegraf agente](metrics-store-custom-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação do clássico, monitoramento e alertas de plataforma

Conforme mencionado anteriormente, clássico de monitoramento e alerta plataforma atualmente utilizável na [seção de alertas (clássico)](monitoring-overview-alerts-classic.md) do Azure portal será desativado em provenientes dado meses eles foram substituídos por sistema mais recente.
Monitorizações e alertas clássicos mais antigos serão retirados em 30 de junho de 2019; incluindo o encerramento de APIs relacionadas, interface do portal do Azure e Serviços. Especificamente, esses recursos serão preteridos:

- Métricas e alertas mais antigos (clássicos) para recursos do Azure como disponíveis atualmente por meio da seção [Alertas (clássico)](monitoring-overview-alerts-classic.md) do portal do Azure; acessível como recurso de [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Plataformas e métricas personalizadas (clássicas) mais antigas para o Application Insights, bem como alertas sobre elas, como estão atualmente disponíveis na seção [Alertas (clássico)](monitoring-overview-alerts-classic.md) do portal do Azure e acessível como recurso de [microsoft.insights / alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Mais antiga alerta anomalias de falha (clássica) está disponível como [detecção inteligente no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) no portal do Azure; com alertas configurados mostrado na [alertas (clássica) seção](monitoring-overview-alerts-classic.md) do Azure Portal

Todos os clássicos de monitoramento e alerta sistemas, incluindo correspondente [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](insights-alerts-powershell.md), [CLI](insights-alerts-command-line-interface.md), [página de portal do Azure, e [domodeloderecursos](monitoring-enable-alerts-using-template.md) permanecerão utilizável até junho de 2019. Após essa data, o monitoramento clássico e o serviço de alertas serão retirados e não estarão mais disponíveis para uso; enquanto quaisquer regras de alerta que continuem a existir em Alertas (clássicos) após junho de 2019 continuarão a ser executadas, mas não estarão disponíveis para modificação.

Quaisquer alertas restantes na plataforma clássica de monitoramento e alertas após junho de 2019 serão automaticamente migrados pela Microsoft para o equivalente na nova plataforma de monitoramento do Azure em julho de 2019. O processo será perfeito sem qualquer tempo de inatividade e garantirá que os clientes não tenham nenhuma perda na cobertura de monitoramento.

Em breve, forneceremos ferramentas para permitir que você migre voluntariamente seus alertas da [seção Alertas (clássica)](monitoring-overview-alerts-classic.md) do portal do Azure para os novos alertas do Azure. Todas as regras configuradas em Alertas (clássicos) que são migradas para o novo Monitor do Azure permanecerão gratuitas e não serão cobradas. As regras de alerta clássicas migradas também não suportarão qualquer cobrança por notificações push por e-mail, webhook ou LogicApp. No entanto, o uso de novos tipos de notificação ou ação (como SMS, Chamada de voz, integração de ITSM, etc.) será cobrado, se adicionado a um alerta novo ou migrado. Para obter mais informações, consulte [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Além disso, a seguir estarão sob o ambit de passíveis de cobrança [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure

Este artigo será links serão atualizadas continuamente & detalhes sobre o monitoramento do Azure novo & alerta de funcionalidade, bem como a disponibilidade de ferramentas para ajudar os usuários a adotar a nova plataforma do Azure Monitor.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../azure-monitor/overview.md).
* Saiba mais sobre os novos [alertas do Azure](monitoring-overview-unified-alerts.md).
