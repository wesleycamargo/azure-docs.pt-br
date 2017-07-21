---
title: "Visão geral dos Alertas no Microsoft Azure e no Azure Monitor | Microsoft Docs"
description: "Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 76c8feb077cca27dc96f43e708cdef4fbb0f824c
ms.contentlocale: pt-br
ms.lasthandoff: 03/31/2017

---
# <a name="what-are-alerts-in-microsoft-azure"></a>O que são os alertas no Microsoft Azure?
Este artigo descreve o que são alertas, seus benefícios e como começar a usá-los. Ele se aplica especificamente ao Azure Monitor, mas fornece ponteiros para outros serviços.  

Os alertas são um método para monitorar as métricas do recurso do Azure, eventos ou logs, e ser notificado quando uma condição especificada é atendida.  

## <a name="alerts-in-different-azure-services"></a>Alertas nos diferentes serviços do Azure
Os alertas estão disponíveis em diferentes serviços, incluindo:

* **Application Insights**: permite os alertas de teste da Web e métricas. Consulte [Definir Alertas no Application Insights](../application-insights/app-insights-alerts.md) e [Monitorar a disponibilidade e a capacidade de resposta de qualquer site](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: Habilita o roteamento de Atividade e Logs de Diagnóstico para o Log Analytics. O Operations Management Suite permite métricas, logs e outros tipos de alerta. Para obter mais informações, consulte [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Azure Monitor**: permite alertas com base nos valores das métricas e nos eventos do log de atividades. Você pode usar a [API REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) para gerenciar alertas.  Para obter mais informações, consulte [Using the Azure portal, PowerShell, or the command-line interface to create alerts](insights-alerts-portal.md).

## <a name="visual-summary"></a>Resumo Visual
O diagrama a seguir resume os alertas e o que você pode fazer com eles especificamente no "Azure Monitor". Outras ações podem estar disponíveis para os serviços listados anteriormente. Por exemplo, atualmente os alertas nos Logs de Diagnóstico só estão disponíveis no Log Analytics.

![Alertas explicados](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>O que pode disparar alertas no Azure Monitor

Você pode receber alertas com base em:

* **Valores da métrica**: esse alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida. Para obter uma lista crescente de métricas disponíveis com suporte do Azure Monitor, consulte [Lista de métricas com suporte no Azure Monitor](monitoring-supported-metrics.md).
* **Eventos do log de atividades**: esse alerta pode disparar quando ocorre um evento específico em um recurso ou quando uma notificações de serviço é postada em sua assinatura.

## <a name="what-can-metric-alerts-do"></a>O que os alertas de métrica podem fazer?
Você pode configurar um alerta para fazer o seguinte:

* Enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* Chamar um webhook, o que permite inicializar ações de automação adicionais. Os exemplos incluem a chamada de:
    - Runbook de Automação do Azure
    - Azure Function
    - Aplicativo lógico do Azure
    - um serviço de terceiros

## <a name="what-can-activity-log-alerts-do"></a>O que os Alertas de Log de Atividades podem fazer?
Você pode configurar um alerta para fazer o seguinte:
* Disparar sempre que um evento específico ocorre em um dos recursos em sua assinatura
* Disparar sempre que uma notificação de serviço é postada em sua assinatura
* Alertar membros de um grupo de ações via
    * sms
    * Email
    * webhook

## <a name="next-steps"></a>Próximas etapas
Obter informações sobre as regras de alerta e sobre como configurá-las usando:

* Saiba mais sobre [Métricas](monitoring-overview-metrics.md)
* Configurar [alertas de métrica por meio do Portal do Azure](insights-alerts-portal.md)
* Configurar o [PowerShell de alertas de métrica](insights-alerts-powershell.md)
* Configurar a [CLI (interface de linha de comando) de alertas de métrica](insights-alerts-command-line-interface.md)
* Configurar a [API REST do Azure Monitor de alertas de métrica](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre o [Log de Atividades](monitoring-overview-activity-logs.md)
* Configurar [alertas do Log de Atividades por meio do Portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas do Log de Atividades por meio do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre as [Notificações de Serviço](monitoring-service-notifications.md)
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)

