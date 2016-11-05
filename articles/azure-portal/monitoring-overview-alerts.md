---
title: Visão geral dos alertas no Microsoft Azure | Microsoft Docs
description: Alerta para monitorar as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada é atendida.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# Visão geral dos alertas no Microsoft Azure
Este artigo descreve o que são alertas, seus benefícios e como começar a usá-los.

## O que são alertas?
Alertas são um método de monitorar as métricas do recurso do Azure, eventos ou logs, em seguida, ser notificado quando uma condição especificada é atendida.

Você pode receber alertas com base em:

* Valores da métrica - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.
* Eventos do log de atividades - um alerta pode disparar em cada evento ou somente quando ocorre um determinado número de eventos.

## Alertas nos diferentes serviços do Azure
Os alertas estão disponíveis em diferentes serviços

* **Application Insights** - permite os alertas de teste da Web e métricas. Consulte [Definir Alertas no Application Insights](../application-insights/app-insights-alerts.md) e [Monitorar a disponibilidade e a capacidade de resposta de qualquer site](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics do OMS** - você pode rotear o Log Analytics para os logs de diagnóstico. O OMS permite a métrica, log e outros tipos de alerta. Para obter mais informações, consulte [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md).
* **Azure Monitor** -o Azure Monitor permite alertas com base nos valores das métricas e nos eventos do log de atividades. O Azure Monitor inclui a [API REST do Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx). Consulte também [Usando o portal do Azure, PowerShell ou Interface da Linha de Comando para criar alertas](../monitoring-and-diagnostics/insights-alerts-portal.md)

## Ações de Alerta
Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* chamar um webhook, que permite inicializar ações de automação adicionais
  
  ![Alertas explicados.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## Próximas etapas
Obter informações sobre as regras de alerta e configurá-las usando

* [O portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [CLI (Interface da linha de comando)](insights-alerts-command-line-interface.md)
* [API REST do Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!---HONumber=AcomDC_0928_2016-->