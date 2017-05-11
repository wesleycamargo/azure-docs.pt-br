---
title: "Limitação de taxa para SMS, emails e webhooks | Microsoft Docs"
description: Seja notificado por SMS, webhook e email quando ocorrerem determinados eventos no Log de atividades.
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 1b4196d3b1d41458c7dd20b6986cc09100ae318c
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>Limitação de taxa para SMS, emails e webhooks
A limitação de taxa é uma suspensão de notificações que ocorre quando um excesso de notificações é enviado para determinado número de telefone ou email. A limitação garante que a comunicação de alertas do log de atividades e de alertas de integridade do serviço são gerenciáveis e acionáveis

As regras de SMS e Email são as mesmas. O limite de taxa para
 - SMS – 10 mensagens em uma hora
 - Email – 100 mensagens em uma hora

## <a name="rate-limit-rules"></a>Regras do limite de taxa
- Um número de telefone ou email específico tem um limite de taxa imposto quando recebe mais do que o limite
- Um número de telefone ou email pode fazer parte de grupos de ações em várias assinaturas. A limitação de taxa aplica-se a todas as assinaturas, ou seja, ela é aplicada assim que o limite é atingido, mesmo se for enviado de várias assinaturas.  
- Quando um número de telefone ou email tem um limite de taxa imposto, uma mensagem adicional do mesmo tipo é enviada para comunicar o limite de taxa. O SMS ou email informa quando o limite de taxa expira.

## <a name="rate-limit-of-webhooks"></a>Limite de taxa de webhooks ##
Não há nenhuma limitação de taxa em vigor para webhooks atualmente.

## <a name="next-steps"></a>Próximas etapas ##
Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md)  
Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas  
Como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md)

