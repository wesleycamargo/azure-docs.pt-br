---
title: Criar alertas do log de atividades | Microsoft Docs
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
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>Criar alertas do log de atividades

## <a name="overview"></a>Visão geral
Os Alertas do Log de Atividades são recursos do Azure e, portanto, podem ser gerenciados com o portal do Azure ou o ARM (Azure Resource Manager). Este artigo mostra como usar o portal do Azure para configurar alertas em eventos do log de atividades.

Você pode receber alertas sobre as operações realizadas em recursos de sua assinatura ou sobre eventos de integridade do serviço que podem afetar a integridade dos recursos. Um Alerta do Log de Atividades monitora os eventos do log de atividades da assinatura específica na qual o alerta é implantado.

Você pode configurar o alerta de acordo com:
* Categoria de Evento (para obter [eventos de integridade do serviço clique aqui](monitoring-activity-log-alerts-on-service-notifications.md))
- Grupo de recursos
- Recurso
- Tipo de recurso
- Nome da operação
- O nível das notificações (Detalhado, Informativo, Aviso, Erro, Crítico)
- Status
- Evento iniciado por

Também é possível configurar para quem o alerta deve ser enviado:
* Selecionar um Grupo de Ação existente
- Criar um novo Grupo de Ação (que pode ser usado posteriormente para futuros alertas)

Saiba mais sobre [Grupos de Ação aqui](monitoring-action-groups.md)

Você pode configurar e obter informações sobre alertas de notificação de integridade do serviço usando
* [Portal do Azure](monitoring-activity-log-alerts.md)
- [Modelos do Gerenciador de Recursos](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Criar um alerta em um evento do log de atividades com um novo grupo de ação com o Portal do Azure
1.    No [portal](https://portal.azure.com), navegue até o serviço **Monitor**

    ![Monitoramento](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Clique na opção **Monitor** para abrir a folha Monitor. Ela abre primeiro na seção **Log de atividades** .

3.    Agora, clique na seção **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Selecione o comando **Adicionar alerta do log de atividades** e preencha os campos

5.    **Dê um nome** ao alerta do log de atividades e escolha uma **Descrição**. Ela será exibida nas notificações enviadas quando esse alerta for acionado.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    A **Assinatura** deve ser preenchida automaticamente para a assinatura que você está usando no momento. Essa é a assinatura na qual o recurso de alerta será implantado e monitorado.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    Escolha o **Grupo de Recursos** ao qual esse alerta será associado na **Assinatura**.

8.    Forneça os valores **Categoria de Evento**, **Grupo de Recursos**, **Recurso**, **Tipo de Recurso**, **Nome da Operação**, **Nível**, **Status** e **Evento iniciado por** para identificar quais eventos esse alerta deve monitorar.

9.    Crie um **Novo** Grupo de Ação dando a ele um **Nome** e um **Nome Curto**; o Nome Curto será referenciado nas notificações enviadas quando esse alerta for ativado.

10.    Em seguida, defina uma lista de destinatários fornecendo os seguintes itens do destinatário

    a. **Nome:** nome do destinatário, alias ou identificador.

    b. **Tipo de Ação:** opte por contatar o destinatário por SMS, email ou webhook

    c. **Detalhes:** de acordo com o tipo de ação escolhido, forneça um número de telefone, endereço de email ou URI de webhook.

11.    Selecione **OK** ao concluir a criação do alerta.

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

Para obter detalhes sobre o esquema de webhook para os alertas do log de atividades, [clique aqui](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>O grupo de ações definido nessas etapas será reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Criar um alerta em um evento do log de atividades para um grupo de ação existente com o Portal do Azure
1.    No [portal](https://portal.azure.com), navegue até o serviço **Monitor**

    ![Monitoramento](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    Clique na opção **Monitor** para abrir a folha Monitor. Ela abre primeiro na seção **Log de atividades** .

3.    Agora, clique na seção **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    Selecione o comando **Adicionar alerta do log de atividades** e preencha os campos

5.    **Dê um nome** ao alerta do log de atividades e escolha uma **Descrição**. Ela será exibida nas notificações enviadas quando esse alerta for acionado.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    A **Assinatura** deve ser preenchida automaticamente para a assinatura que você está usando no momento.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    Escolha o **Grupo de Recursos** para esse alerta.

8.    Forneça os valores **Categoria de Evento**, **Grupo de Recursos**, **Recurso**, **Tipo de Recurso**, **Nome da Operação**, **Nível**, **Status** e **Evento iniciado por** para definir o escopo de quais eventos esse alerta deve ser aplicado.

9.    Escolha **Notificar Por** um **Grupo de ação existente**. Selecione o respectivo grupo de ação.

10.    Selecione **OK** ao concluir a criação do alerta.

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção Alertas do serviço Monitor. Selecione o alerta que você deseja gerenciar para poder:
* **Editá-lo**.
* **Excluí-lo**.
* **Desabilitá-lo** ou **Habilitá-lo** se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas:
Obtenha uma [visão geral dos alertas](monitoring-overview-alerts.md)  
Examine o [esquema do webhook de alertas do log de atividades](monitoring-activity-log-alerts-webhook.md) Saiba mais sobre [grupos de ação](monitoring-action-groups.md)  
Saiba mais sobre as [Notificações de integridade do serviço](monitoring-service-notifications.md)

