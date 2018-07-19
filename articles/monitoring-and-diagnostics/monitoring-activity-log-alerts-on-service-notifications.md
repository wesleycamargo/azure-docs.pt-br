---
title: Receber alertas do log de atividades nas notificações de serviço do Azure
description: Seja notificado por SMS, email ou webhook quando um serviço do Azure for executado.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 1e79fee75b2361dd7da8c46c175a5a6532089ad6
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437232"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do log de atividades em notificações de serviço
## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação do serviço de integridade (Problemas de serviço, Manutenção planejada, Avisos de integridade).
- A assinatura afetada.
- Os serviços afetados.
- As regiões afetadas.

> [!NOTE]
> As notificações de integridade do serviço não enviam um alerta sobre o recurso de eventos de integridade.

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

4. Selecione a **Assinatura**, os **Serviços** e as **Regiões** sobre os quais você deseja ser alertado.

    ![A caixa de diálogo "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Esta assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado para essa assinatura e monitora os eventos no log de atividades para ele.

5. Escolha os **Tipos de evento** sobre os quais você deseja ser alertado: *Problema de serviço*, *Manutenção planejada* e *Consultorias de integridade* 

6. Defina os detalhes do alerta inserindo um **Nome de regra de alerta** e uma **Descrição**.

7. Selecione o **Grupo de recursos** onde você deseja que o alerta seja salvo.

8. Crie um grupo de ação selecionando **Novo grupo de ação**. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, insira um nome na caixa **Nome curto**. O nome curto é referenciado nas notificações enviadas quando esse alerta é acionado.

    ![Criar um novo grupo de ações](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Defina uma lista de destinatários fornecendo os seguintes itens do destinatário:

    a. **Nome**: nome, alias ou identificador do destinatário.

    b. **Tipo de Ação**: selecione SMS, email, webhook, aplicativo do Azure e muito mais.

    c. **Detalhes**: de acordo com o tipo de ação escolhido, insira um número de telefone, endereço de email, URI de webhook, etc.

10. Selecione **OK** para criar o grupo de ação e, em seguida, **Criar regra de alerta** para concluir o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

Saiba como [Configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](../service-health/service-health-alert-webhook-guide.md). Para saber mais sobre o esquema do webhook para alertas de log de atividades, veja [Webhooks para alertas do log de atividades do Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta em uma notificação de integridade do serviço para um grupo de ação existente usando o portal do Azure

1. Siga as etapas 1 a 7 na seção anterior para criar a notificação de integridade do serviço. 

2. Em **Definir grupo de ação**, clique no botão **Selecionar grupo de ação**. Selecione o grupo de ação apropriado.

3. Selecione **Adicionar** para adicionar o grupo de ação e, em seguida, **Criar regra de alerta** para concluir o alerta.

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
