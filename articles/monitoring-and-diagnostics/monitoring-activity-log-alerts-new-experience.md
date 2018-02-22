---
title: "Criar alertas do log de atividades e gerenciá-los usando a nova experiência de Alertas (versão prévia) no Monitor Azure| Microsoft Docs"
description: "Este artigo fornece informações sobre como criar alertas do log de atividades a partir da guia Alertas (versão prévia) no Azure Monitor. Este artigo detalha a nova experiência do usuário para esse recurso."
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: afdd82617c47f0dee22c229feba87bdf79b90a69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Crie alertas do log de atividades usando a nova experiência de Alertas (versão prévia)

Alertas do log de atividades são os alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta.

Esses alertas são para recursos do Azure e podem ser criados usando um modelo do Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Este artigo apresenta os conceitos por trás de alertas de log de atividades. Em seguida, mostra como usar o Portal do Azure para configurar um alerta nos eventos de log de atividades usando a nova experiência em [Alertas do Azure (versão prévia)](monitoring-overview-unified-alerts.md).

Normalmente, você cria alertas do log de atividades para receber notificações quando ocorrem alterações específicas nos recursos da sua assinatura do Azure, muitas vezes com escopo para recursos de recursos específicos. Por exemplo, talvez você queira ser notificado quando qualquer máquina virtual em (grupo de recursos de exemplo) **myProductionResourceGroup** for excluída, ou ser notificado se quaisquer novas funções forem atribuídas a um usuário em sua assinatura.

Você pode configurar um alerta do log de atividades com base em qualquer propriedade de nível superior no objeto JSON de um evento do log de atividades. No entanto, o portal mostra as opções mais comuns, conforme detalhado nas seções a seguir:

>[!NOTE]

> Quando a categoria for "administrativa", você deverá especificar pelo menos um dos critérios anteriores no seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.
>

Quando um alerta do log de atividades é ativado, ele usa um grupo de ações para gerar ações ou notificações. Um grupo de ações é um conjunto reutilizável de destinatários de notificação, como endereços de email, URLs de webhook ou números de telefone de SMS. Os destinatários podem ser referenciados de vários alertas para centralizar e agrupar seus canais de notificação. Quando você define o alerta do log de atividades, tem duas opções. Você pode:

* Use um grupo existente no seu alerta do log de atividades.
* Crie um novo grupo de ações.

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](monitoring-action-groups.md).

Para saber mais sobre as notificações de integridade do serviço, veja [Receber alertas do log de atividades sobre as notificações de integridade do serviço](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>O que há de novo na versão prévia de Alertas para logs de atividades?

[Alertas do Azure (versão prévia)](monitoring-overview-unified-alerts.md) agora fornece experiência do usuário avançada para alertas do log de atividades. Com a [experiência do usuário avançada para Alertas](monitoring-overview-unified-alerts.md), agora você pode:

- [Criar](#create-an-alert-rule-for-an-activity-log) e [gerenciar](#view-and-manage-activity-log-alert-rules) as regras de alerta do log de atividades a partir da folha **Monitor** > **Alertas (versão prévia)**. Saiba mais sobre [Logs de Atividades](monitoring-overview-activity-logs.md).

- **Novas opções para o Destino de Alertas**:  ao criar uma nova regra de alerta do log de atividades, agora é possível selecionar um recurso de destino, um grupo de recursos ou uma assinatura.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Criar uma regra de alerta para um log de atividades

> [!NOTE]

>  Ao criar as regras de alerta, assegure o seguinte:

> - A assinatura no escopo não é diferente da assinatura onde o alerta foi criado.
- Os critérios devem ser nível/status/chamador/grupo de recursos/ID do recurso/tipo de recurso/categoria de eventos no qual o alerta é configurado.
- Não há nenhuma condição "anyOf" ou condições aninhadas na configuração de alerta JSON (basicamente, apenas um allOf é permitido sem nenhum allOf/anyOf adicional).


Use este procedimento:

1. No Portal do Azure, selecione **Monitor** > **Alertas (versão prévia).**
2. Clique em **Nova Regra de Alerta**na parte superior da janela **Alertas (versão prévia)**.

     ![nova regra de alerta](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     A janela **Criar regra** é exibida.

      ![opções de nova regra de alerta](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. Em **Definir a condição de alerta**, forneça as informações a seguir e clique em **Concluído**.

    - **Destino de Alerta:** para exibir e selecionar o destino do novo alerta, use **Filtrar por assinatura** / **Filtrar por tipo de recurso** e selecione o recurso ou grupo de recursos da lista exibida.

    > [!NOTE]

    > É possível selecionar um recurso, um grupo de recursos ou uma assinatura inteira.

    **Exibição de exemplo do destino de alerta**

     ![Selecionar o destino](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - Em ** Critérios de destino, clique em **adicionar critérios**, selecione o tipo de sinal como **Log de atividades**.

    - Selecione o sinal da lista exibida.

    É possível selecionar a linha do tempo do histórico de log e a lógica de alerta correspondente para esse sinal de destino:

    **Tela Adicionar critérios**

    ![adicionar critérios](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Tempo de histórico**: nas últimas 6/12/24 horas, na última semana.

    **Lógica de Alerta**:

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Em **definir regras de alerta,** forneça os detalhes a seguir:

    - **Nome da regra de alerta** – nome da nova regra de alerta
    - **Descrição** – Descrição para a nova regra de alerta
    - **Salvar alerta para o grupo de recursos** – Selecione o Grupo de Recursos, onde deseja salvar essa nova regra.

5. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Alternativamente, [crie um novo grupo de ações](monitoring-action-groups.md) e atribua à nova regra. Para criar um novo grupo, clique em **+ Novo grupo**.

6. Para habilitar as regras após criá-lo, clique em **Sim** da opção **Habilitar regra após a criação**.
7. Clique em **Criar regra de alerta**.

    A nova regra de alerta para o log de atividades é criada e uma mensagem de confirmação aparece no canto superior direito da janela.

    ![ regra de alerta criada](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    É possível habilitar, desabilitar, editar ou excluir uma regra. [Saiba mais](#view-and-manage-activity-log-alert-rules) sobre o gerenciamento de regras do log de atividades.

## <a name="view-and-manage-activity-log-alert-rules"></a>Exibir e gerenciar as regras de alerta do log de atividades

1. Do Portal do Azure, clique em **Monitor** > **Alertas (versão prévia)** e clique em **Gerenciar regras** na parte superior esquerda da janela.

    ![ gerenciar regras de alerta](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    A lista das regras disponíveis é exibida.

2. Pesquise a regra do log de atividades para modificar.

    ![ pesquisar regras de alerta do log de atividades](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Você pode usar os filtros disponíveis - **Assinatura** , **Grupo de recursos** ou **Recursos** para localizar a regra de atividade que deseja editar.

    > [!NOTE]

    > É possível editar apenas a **Descrição**, os **Critérios de destino** e os **Grupos de ações**.

3.  Selecione a regra e clique duas vezes para editar as opções da regra. Faça as alterações necessárias e clique em **Salvar**.

    ![ gerenciar regras de alerta](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  É possível desabilitar, habilitar ou excluir uma regra. Selecione a opção apropriada na parte superior da janela após selecionar a regra, conforme detalhado na etapa 2.


## <a name="next-steps"></a>Próximas etapas

- [Arquivar aletras do log de atividades](monitoring-archive-activity-log.md)
- [Transmitir logs de Atividades para Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
- [Migar para logs de Atividades](monitoring-migrate-management-alerts.md)
