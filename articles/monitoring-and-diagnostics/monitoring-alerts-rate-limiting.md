---
title: "Limitação de taxa para SMS, emails, notificações por push do Azure App e webhooks | Microsoft Docs"
description: "Entenda como o Azure limita o número de notificações possíveis de webhook, email, push do Azure App ou SMS de um grupo de ações."
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/16/2018
ms.author: dukek
ms.openlocfilehash: a4f97cc79945d266edd0af577e37fc9da2aa97bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitação de taxa para mensagens de SMS, emails, notificações por push do Azure App e webhooks
A limitação de taxa é uma suspensão de notificações que ocorre quando um excesso de notificações é enviado para determinado número de telefone, endereço de email ou dispositivo. A limitação de taxa assegura que os alertas sejam gerenciáveis e acionáveis.

Os limites de taxa são:

 - **SMS**: não mais de 1 SMS a cada 5 minutos.
 - **Email**: não mais de 100 emails em uma hora.
 
 Outras ações não são limitadas por classificação.

## <a name="rate-limit-rules"></a>Regras do limite de taxa
- Um número de telefone ou email específico tem um limite de taxa imposto quando recebe mais mensagens do que o limite permite.
- Um número de telefone ou email pode fazer parte de grupos de ações em várias assinaturas. A limitação de taxa aplica-se a todas as assinaturas. Ela se aplica assim que o limite é atingido, mesmo se as mensagens forem enviadas de várias assinaturas.
- Quando um endereço de email tem uma limitação de taxa, uma notificação adicional é enviada para comunicar a limitação de taxa. A notificação indica quando a limitação de taxa expira.

## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md).
* Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md).
