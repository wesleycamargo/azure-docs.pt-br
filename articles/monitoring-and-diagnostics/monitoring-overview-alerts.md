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
ms.date: 05/15/2018
ms.author: robb
ms.openlocfilehash: 039ab7226b4ea7e011e1c0cbb4cac528b5237483
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203998"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte para [alertas de métrica em tempo quase real mais recentes e uma nova experiência de alertas](monitoring-overview-unified-alerts.md). 
>

Os alertas permitem que você configure condições sobre dados e seja notificado quando as condições corresponderem aos dados de monitoramento mais recentes.


## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre dados do Azure Monitor
Há dois tipos de alertas clássicos disponíveis - alertas de métrica e alertas de registro de atividades.

* **Alertas de métrica clássicos**: este alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limite for ultrapassado, e a condição do alerta for atendida), e também quando for "Resolvido" (quando o limite for ultrapassado novamente e a condição não for mais atendida). Para alertas de métrica mais novos, consulte abaixo.

* **Alertas do log de atividade clássicos** – um alerta do log de streaming que dispara quando um evento do Log de Atividades for gerado correspondendo aos critérios do filtro que você atribuiu. Esses alertas têm apenas um estado, "Ativado", pois o mecanismo de alerta simplesmente aplica os critérios do filtro a qualquer evento novo. Esses alertas podem ser usados para receber uma notificação quando um novo incidente de Integridade do Serviço ocorrer, ou quando um usuário ou aplicativo executar uma operação em sua assinatura, por exemplo, "Excluir a máquina virtual".

Para dados de Log de Diagnóstico disponíveis por meio do Azure Monitor, é recomendável rotear os dados para o Log Analytics (anteriormente OMS) e usar um alerta de consulta do Log Analytics. O Log Analytics agora usa o [novo método de alerta](monitoring-overview-unified-alerts.md) 

O diagrama a seguir resume as fontes de dados no Azure Monitor e, conceitualmente, como emitir um alerta com base nesses dados.

![Alertas explicados](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomia de alertas do Azure Monitor (clássico)
O Azure usa os termos a seguir para descrever alertas clássicos e suas funções:
* **Alerta** – uma definição de critérios (uma ou mais regras ou condições) que são ativados quando atendidos.
* **Ativo** – o estado quando os critérios definidos por um alerta clássico são atendidos.
* **Resolvido** – o estado quando os critérios definidos por um alerta clássico não forem mais atendidos após terem sido atendidos anteriormente.
* **Notificação** – a ação realizada após a ativação de um alerta clássico.
* **Ação** – uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando por email um endereço ou publicando em uma URL de webhook). As notificações normalmente podem disparar várias ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como eu recebo uma notificação de um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure de serviços diferentes usavam seus próprios métodos de notificação internos. 

Agora, o Azure Monitor oferece um agrupamento de notificação reutilizável chamado *Grupos de Ações*. Os Grupos de Ações especificam um conjunto de receptores para uma notificação e sempre que um alerta é ativado que faça referência ao grupo de ação, todos os receptores recebem essa notificação. Isso permite que você reutilize um agrupamento de destinatários (por exemplo, sua lista de engenheiros em serviço) em vários objetos de alerta. Os Grupos de Ações dão suporte para notificação, postando em uma URL do webhook além de endereços de email, números de SMS e várias outras ações.  Para obter mais informações, consulte [Grupos de Ações](monitoring-action-groups.md). 

Os alertas de Log de Atividades clássico mais antigos usam Grupos de Ações.

No entanto, os alertas de métrica mais antigos não usam Grupos de Ações. Em vez disso, é possível configurar as ações a seguir: 
* Enviar notificações por email para o administrador do serviço, coadministradores e/ou emails adicionais especificados.
* Chamar um webhook, o que permite inicializar ações de automação adicionais.

Webhooks permite automação e correção, por exemplo, usando:
    - Runbook de Automação do Azure
    - Azure Function
    - Aplicativo lógico do Azure
    - um serviço de terceiros

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
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)
* Configurar [Alertas mais recentes](monitor-alerts-unified-usage.md)
