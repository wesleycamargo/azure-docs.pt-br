---
title: "Visão geral dos alertas no Microsoft Azure | Microsoft Docs"
description: "Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f80179a487d08244cbd5e57d34b10c4ab67b3cf5


---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Visão geral dos alertas no Microsoft Azure
Este artigo descreve o que são alertas, seus benefícios e como começar a usá-los.  

## <a name="what-are-alerts"></a>O que são alertas?
Alertas são um método de monitorar as métricas do recurso do Azure, eventos ou logs, em seguida, ser notificado quando uma condição especificada é atendida.

Você pode receber alertas com base em:

* **Valores da métrica**: esse alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.
* **Eventos do log de atividades**: esse alerta pode disparar em cada evento ou somente quando ocorre determinado número de eventos.

## <a name="alerts-in-different-azure-services"></a>Alertas nos diferentes serviços do Azure
Os alertas estão disponíveis em diferentes serviços, incluindo:

* **Application Insights**: permite os alertas de teste da Web e métricas. Consulte [Definir Alertas no Application Insights](../application-insights/app-insights-alerts.md) e [Monitorar a disponibilidade e a capacidade de resposta de qualquer site](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)**: habilita o roteamento de logs de diagnóstico para o Log Analytics. O Operations Management Suite permite métricas, logs e outros tipos de alerta. Para obter mais informações, consulte [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Azure Monitor**: permite alertas com base nos valores das métricas e nos eventos do log de atividades. O Azure Monitor inclui a [API REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).  Para obter mais informações, consulte [Using the Azure portal, PowerShell, or the command-line interface to create alerts](insights-alerts-portal.md).

## <a name="alert-actions"></a>Ações de alerta
Você pode configurar um alerta para fazer o seguinte:

* Enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* Chamar um webhook, o que permite inicializar ações de automação adicionais.
  
  ![Alertas explicados](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>Próximas etapas
Obter informações sobre as regras de alerta e sobre como configurá-las usando:

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI (Interface da linha de comando)](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)




<!--HONumber=Nov16_HO3-->


