---
title: "Criar e gerenciar Grupos de Ação no Portal do Azure | Microsoft Docs"
description: 
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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9a290422bf897397942fccf2e3733d0709701e91
ms.lasthandoff: 03/31/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Criar e gerenciar Grupos de Ação no Portal do Azure
## <a name="overview"></a>Visão geral ##
Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Os grupos de ações permitem configurar uma lista de destinatários. Esses grupos podem, então, ser utilizados ao definir alertas de log de atividades, garantindo que um grupo de ação específico seja notificado quando o alerta de log de atividades for disparado.

Um grupo de ação pode ter até 10 ações de cada tipo. Uma ação é definida pela combinação de:

**Nome:** um identificador exclusivo dentro do grupo de ação.  
**Tipo de ação:** define a ação que será executada. As opções são enviar SMS, enviar email ou chamar um Webhook.  
**Detalhes:** com base no tipo de ação, é necessário fornecer o número de telefone, endereço de email ou URI de webhook correspondente.

É possível configurar e obter informações sobre alertas de notificação de integridade do serviço usando:
* [Portal do Azure](monitoring-action-groups.md)
- [Modelos do Gerenciador de Recursos](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>Criar um grupo de ação para o portal do Azure ##
1.    No [portal](https://portal.azure.com), navegue até o serviço **Monitor**

    ![Monitoramento](./media/monitoring-action-groups/home-monitor.png)
2.    Clique na opção **Monitor** para abrir a folha Monitor. Esta folha reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

3.    Agora, clique na seção **Grupos de ação**

    ![Action-Group](./media/monitoring-action-groups/action-groups-blade.png)
4.    Clique no comando do grupo de ação **Adicionar** e preencha os campos

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.    Forneça um **Nome** e um **Nome curto** para o grupo de ação; o nome curto será referido em notificações enviadas a esse grupo

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.    A **Assinatura** é aquela em que o grupo de ação será salvo. Ela será preenchida automaticamente com a assinatura que você está usando no momento.

7.    Escolha o **Grupo de Recursos** para esse grupo de ação.

8.    Em seguida, defina uma lista de ações por meio de uma combinação de:
  1. **Nome:** um identificador exclusivo dentro do grupo de ação.
  2. **Tipo de ação:** define a ação que será executada. As opções são enviar SMS, enviar email ou chamar um Webhook.
  3. **Detalhes:** com base no tipo de ação, é necessário fornecer o número de telefone, endereço de email ou URI de webhook correspondente.

9.    Selecione **OK** após concluir a criação do grupo de ação.

## <a name="managing-your-action-groups"></a>Gerenciando seus grupos de ação ##
Após você ter criado um grupo de ação, ele ficará visível na seção de Grupos de ação do serviço de Monitor. Selecione o grupo de ação que você deseja gerenciar, de modo a poder:
* Adicionar, editar ou remover destinatários.
-    Excluir o grupo de ação.

## <a name="next-steps"></a>Próximas etapas: ##
Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md)  
Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](monitoring-activity-log-alerts-webhook.md)  
Saiba mais sobre a [limitação de taxa](monitoring-alerts-rate-limiting.md) para alertas  
Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas  
Como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md)

