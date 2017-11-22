---
title: "Receber alertas do log de atividades nas notificações de serviço do Azure | Microsoft Docs"
description: "Seja notificado por SMS, email ou webhook quando um serviço do Azure for executado."
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 6e011ea3d9d8f8453068d43e390cfba46dfb3277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do log de atividades em notificações de serviço
## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação do serviço de integridade (Problemas de serviço, Manutenção planejada, Avisos de integridade).
- A assinatura afetada.
- Os serviços afetados.
- As regiões afetadas.

Também é possível configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ações existente.
- Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](monitoring-action-groups.md).

Para saber mais sobre como configurar alertas de notificação de integridade do serviço usando modelos do Azure Resource Manager, consulte [modelos do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure
1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.

    ![O serviço “Integridade do Serviço”](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. Na seção **Alertas**, selecione **Alertas de integridade**.

    ![A guia “Alertas de integridade”](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Selecione **Criar alerta de integridade do serviço** e preencha os campos.

    ![O comando “Criar alerta de integridade do serviço”](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Insira um nome na caixa **Nome do alerta do log de atividades** e forneça uma **Descrição**.

    ![A caixa de diálogo "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado para essa assinatura e monitora os eventos no log de atividades para ele.

6. Selecione o **Grupo de recursos** no qual o recurso de alerta é criado. Este não é o grupo de recursos monitorado pelo alerta. Em vez disso, é o grupo de recursos onde se encontra o recurso de alerta.

7. A caixa **Categoria de evento** é definida automaticamente como **Integridade do Serviço**. Opcionalmente, selecione as notificações **Serviço**, **Região** e **Tipo** de integridade do serviço que deseja receber.

8. Em **Alerta via**, selecione o botão **Novo** grupo de ações. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, insira um nome na caixa **Nome curto**. O nome curto é referenciado nas notificações enviadas quando esse alerta é acionado.

9. Defina uma lista de destinatários fornecendo os seguintes itens do destinatário:

    a. **Nome**: nome, alias ou identificador do destinatário.

    b. **Tipo de Ação**: selecione SMS, email, webhook, aplicativo do Azure e muito mais.

    c. **Detalhes**: de acordo com o tipo de ação escolhido, insira um número de telefone, endereço de email, URI de webhook, etc.

10. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

Saiba como [Configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](../service-health/service-health-alert-webhook-guide.md). Para saber mais sobre o esquema do webhook para alertas de log de atividades, veja [Webhooks para alertas do log de atividades do Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta em uma notificação de integridade do serviço para um grupo de ação existente usando o portal do Azure

1. Siga as etapas 1 a 7 na seção anterior para criar a notificação de integridade do serviço. 

2. Em **Alerta via**, selecione o botão Grupo de ações **existente**. Selecione o grupo de ação apropriado.

3. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção **Alertas** do **Monitor**. Selecione o alerta que você deseja gerenciar:

* Edite-o.
* Exclua-o.
* Desabilite-o ou habilite-o, se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](../service-health/service-health-alert-webhook-guide.md).
- Saiba mais sobre as [notificações de integridade do serviço](monitoring-service-notifications.md).
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md).
- Examine o [esquema do webhook de alertas de log de atividades](monitoring-activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas. 
- Saiba mais sobre [grupos de ação](monitoring-action-groups.md).
