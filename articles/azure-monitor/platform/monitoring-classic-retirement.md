---
title: O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos
description: Visão geral da desativação de serviços e funcionalidade de monitoramento clássicos, mostrada anteriormente no portal do Azure em Alertas (clássico). O alerta e monitoramento clássico inclui alertas métricos clássicos para recursos do Azure, alertas de métricas clássicas para Application Insights, alertas de webtest clássicos para Application Insights, alertas baseados em métricas personalizadas clássicas para Application Insights e alertas clássicos para o SmartDetection v1 do Application Insights
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 15a3073cde3f9e9ec8c70212cc3b1a591e703915
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052216"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

O Azure Monitor se tornou um serviço unificado de monitoramento de pilha completa, que agora dá suporte a "Uma métrica" e "Um alerta" nos recursos. Para obter mais informações, confira nossa [postagem sobre o novo Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram desenvolvidas para serem mais rápidas, mais inteligentes e extensíveis – acompanhando a crescente expansão da computação em nuvem e em linha com a filosofia de Nuvem Inteligente da Microsoft. 

Com a nova plataforma de monitoramento e alerta do Azure em vigor, aposentaremos a plataforma de monitoramento e alerta "clássica" - hospedada na seção *visualizar alertas clássicos* dos alertas do Azure, **será suspenso até junho de 2019**.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma. Para os clientes que têm um grande número de alertas, estamos trabalhando para fornecer uma maneira automatizada de mover os alertas clássicos existentes para o novo sistema de alertas sem interrupções ou custos adicionais.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicativos

Agora, a plataforma de métrica mais recente do Monitor do Azure agora ativará o monitoramento proveniente do Application Insights. Essa movimentação significa que o Application Insights se conectará a Action Groups, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem acionar chamadas de voz, funções do Azure, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e os Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que os usuários do Application Insights aproveitem a mesma tecnologia que potencializa o monitoramento em outros recursos do Azure e sustenta o monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para Application Insights abrangerá:

- **Métricas do Application Insights plataforma** – que fornece métricas predefinidas populares do produto do Application Insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights Disponibilidade e teste na Web** - que fornece a capacidade de avaliar a capacidade de resposta e a disponibilidade de seu aplicativo ou servidor da web. Para obter mais informações, consulte este artigo sobre o uso de [Testes de disponibilidade e alertas para o Application Insights no novo Monitor do Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Métricas do aplicativo Insights personalizado** – que permite definir e emitem suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar [métrica personalizada para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente)** - que notifica automaticamente em tempo real se o seu aplicativo da Web sofrer um aumento anormal na taxa de solicitações HTTP com falha ou chamadas de dependência. Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente) como parte do novo Monitor do Azure, estarão disponíveis em breve e atualizaremos este documento com links na próxima iteração à medida que for sendo implementado nos próximos meses.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Unificada métricas e alertas para outros recursos do Azure

Desde março de 2018, a próxima geração de monitoramento multidimensional e de alertas para recursos do Azure está em disponibilidade. Agora, a nova plataforma métrica e os alertas são mais rápidos com recursos quase em tempo real. Mais importante ainda, os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais nova inclui a opção de dimensões, que permitem dividir e filtrar para combinação, condição ou operação de valor específico. Como todos os alertas no novo Monitor do Azure, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups - permitindo que as notificações se expandam além do e-mail ou webhook para SMS, voz, função do Azure, Runbook de automação e muito mais.
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

Em breve, forneceremos ferramentas para permitir que você migre voluntariamente seus alertas da [seção Alertas (clássica)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure para os novos alertas do Azure. Todas as regras configuradas em Alertas (clássicos) que são migradas para o novo Monitor do Azure permanecerão gratuitas e não serão cobradas. As regras de alerta clássicas migradas também não suportarão qualquer cobrança por notificações push por e-mail, webhook ou LogicApp. No entanto, o uso de novos tipos de notificação ou ação (como SMS, Chamada de voz, integração de ITSM, etc.) será cobrado, se adicionado a um alerta novo ou migrado. Para obter mais informações, consulte [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Além disso, a seguir estarão sob o ambit de passíveis de cobrança [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure

Este artigo será links serão atualizadas continuamente & detalhes sobre o monitoramento do Azure novo & alerta de funcionalidade, bem como a disponibilidade de ferramentas para ajudar os usuários a adotar a nova plataforma do Azure Monitor.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../../azure-monitor/overview.md).
* Saiba mais sobre os novos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
