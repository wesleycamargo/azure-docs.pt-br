---
title: Visão geral dos alertas clássicos no Microsoft Azure e no Azure Monitor | Microsoft Docs
description: Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte a [alertas de métrica quase em tempo real mais recentes](monitoring-overview-unified-alerts.md)
>

Este artigo descreve as várias fontes de alertas no Microsoft Azure, as finalidades desses alertas, seus benefícios e como começar a usá-los. Aplica-se especificamente aos alertas clássicos do Azure Monitor. Os alertas oferecem um método de monitoramento no Azure que permite a configuração de condições sobre os dados e o recebimento de notificações quando as condições correspondem aos últimos dados de monitoramento.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taxonomia de alertas clássicos do Azure Monitor
O Azure usa os termos a seguir para descrever alertas clássicos e suas funções:
* **Alerta** – uma definição de critérios (uma ou mais regras ou condições) que são ativados quando atendidos.
* **Ativo** – o estado quando os critérios definidos por um alerta clássico são atendidos.
* **Resolvido** – o estado quando os critérios definidos por um alerta clássico não forem mais atendidos após terem sido atendidos anteriormente.
* **Notificação** – a ação realizada após a ativação de um alerta clássico.
* **Ação** – uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando por email um endereço ou publicando em uma URL de webhook). As notificações normalmente podem disparar várias ações.


## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre dados do Azure Monitor
Há três tipos de alertas de dados disponíveis no Azure Monitor – alertas de métrica, alertas de métrica quase em tempo real e alertas do Log de Atividades.

* **Alertas de métrica clássicos**: este alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limite for ultrapassado, e a condição do alerta for atendida), e também quando for "Resolvido" (quando o limite for ultrapassado novamente e a condição não for mais atendida). Esses são alertas de métrica mais antigos. Para alertas de métrica mais novos, consulte abaixo.

* **Alertas de métrica quase em tempo real** (nova experiência de alerta) - Esses são a nova geração de alertas de métrica com recursos aprimorados quando comparados aos alertas de métrica anteriores. Esses alertas podem ser executados em uma frequência de 1 minuto. Eles também dão suporte ao monitoramento de várias métricas (atualmente duas).  O alerta gera uma notificação quando é “Ativado” (quando os limites de cada métrica são ultrapassados ao mesmo tempo e a condição do alerta é atendida) e também quando é “Resolvido” (quando, pelo menos, uma métrica ultrapassa o limite novamente e a condição não é mais atendida).

* **Alertas do log de atividade clássicos** – um alerta do log de streaming que dispara quando um evento do Log de Atividades for gerado correspondendo aos critérios do filtro que você atribuiu. Esses alertas têm apenas um estado, "Ativado", pois o mecanismo de alerta simplesmente aplica os critérios do filtro a qualquer evento novo. Esses alertas podem ser usados para receber uma notificação quando um novo incidente de Integridade do Serviço ocorrer, ou quando um usuário ou aplicativo executar uma operação em sua assinatura, por exemplo, "Excluir a máquina virtual".

Para dados de Log de Diagnóstico disponíveis por meio do Azure Monitor, sugerimos rotear os dados para o Log Analytics e usando um alerta do Log Analytics. O diagrama a seguir resume as fontes de dados no Azure Monitor e, conceitualmente, como emitir um alerta com base nesses dados.

![Alertas explicados](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Como fazer para receber uma notificação em um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure de serviços diferentes usavam seus próprios métodos de notificação internos. De agora em diante, o Azure Monitor oferece um agrupamento de notificação reutilizável chamado de Grupos de Ações. Os Grupos de Ação especificam um conjunto de destinatários para uma notificação – qualquer quantidade de endereços de email, números de telefone (para SMS) ou URLs de webhook – e sempre que um alerta for ativado fazendo referência ao Grupo de Ações, todos os destinatários receberão essa notificação. Isso permite que você reutilize um agrupamento de destinatários (por exemplo, sua lista de engenheiros em serviço) em vários objetos de alerta. Atualmente, apenas os alertas do Log de Atividades usam os Grupos de Ação, mas vários outros tipos de alerta do Azure também estão trabalhando para usar os Grupos de Ação.

Grupos de Ação dão suporte à notificação postando uma URL de webhook além dos endereços de email e números SMS. Isso permite a automação e a correção, por exemplo, usando:
    - Runbook de Automação do Azure
    - Azure Function
    - Aplicativo lógico do Azure
    - um serviço de terceiros

Os alertas de métrica quase em tempo real e os alertas do Log de Atividades usam Grupos de Ação.

Os alertas de métrica mais antigos disponíveis em Alertas (Clássicos) não usam Grupos de Ações. Em um alerta de métrica individual, você pode configurar notificações para:
* Enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* Chamar um webhook, o que permite inicializar ações de automação adicionais.

## <a name="next-steps"></a>Próximas etapas
Obter informações sobre as regras de alerta e sobre como configurá-las usando:

* Saiba mais sobre [Métricas](monitoring-overview-metrics.md)
* Configurar [alertas de métrica clássicos por meio do portal do Azure](insights-alerts-portal.md)
* Configurar [PowerShell de alertas de métrica clássicos](insights-alerts-powershell.md)
* Configurar a [CLI (interface de linha de comando) de alertas de métrica clássicos](insights-alerts-command-line-interface.md)
* Configurar a [API REST do Azure Monitor de alertas de métrica clássicos](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre o [Log de Atividades](monitoring-overview-activity-logs.md)
* Configurar [alertas do Log de Atividades por meio do Portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas do Log de Atividades por meio do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre os [Alertas de métrica mais novos](monitoring-near-real-time-metric-alerts.md)
* Saiba mais sobre as [Notificações de Serviço](monitoring-service-notifications.md)
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)
* Configurar [Alertas](monitor-alerts-unified-usage.md)
