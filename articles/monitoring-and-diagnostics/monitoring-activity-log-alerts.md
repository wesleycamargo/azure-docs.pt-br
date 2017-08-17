---
title: Criar alertas do log de atividades | Microsoft Docs
description: Seja notificado por SMS, webhook e email quando ocorrerem determinados eventos no Log de atividades.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>Criar alertas do log de atividades

## <a name="overview"></a>Visão geral
Alertas de Log de Atividades são alertas ativados quando ocorre um novo evento de Log de Atividades que corresponde às condições especificadas no alerta. Eles são recursos do Azure, portanto podem ser criados usando um modelo do Resource Manager, e criados, atualizados ou excluídos no Portal do Azure. Este artigo apresenta os conceitos por trás dos alertas de Log de Atividades e, depois, mostra como usar o Portal do Azure para configurar um alerta em eventos do Log de Atividades.

Normalmente, os alertas de Log de Atividades são criados para uma dentre duas finalidades:
1. Para receber uma notificação quando ocorrerem alterações específicas nos recursos de sua assinatura do Azure, normalmente com escopo para recursos ou grupos de recursos específicos. Por exemplo, convém ser notificado quando qualquer VM em myProductionResourceGroup for excluída. Ou, você pode receber uma notificação se quaisquer funções novas forem atribuídas a um usuário em sua assinatura.
2. Para receber uma notificação quando um evento de Integridade de Serviço ocorrer. Eventos de Integridade de Serviço incluem uma notificação de incidentes e eventos de manutenção que se aplicam aos recursos em sua assinatura.

Em ambos os casos, o alerta do Log de Atividades só monitorará eventos na assinatura na qual o alerta foi criado.

Você pode configurar um alerta do Log de Atividades com base em qualquer propriedade de nível superior no objeto JSON de um evento do Log de Atividades. No entanto, o Portal mostra as opções mais comuns:
- **Categoria** – pode ser Administrativa, Integridade do Serviço, Dimensionamento Automático, Recomendação. [Confira este artigo para saber mais sobre categorias do Log de Atividades](./monitoring-overview-activity-logs.md#categories-in-the-activity-log) e [saiba mais sobre os eventos de integridade do serviço aqui](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupo de recursos**
- **Recurso**
- **Tipo de recurso**
- **Nome da operação** – o nome da operação RBAC do Gerenciador de Recursos.
- **Nível** – o nível de gravidade do evento (Detalhado, Informativo, Aviso, Erro, Crítico)
- **Status** – o status do evento, normalmente "Iniciado", "Falha" ou "Êxito"
- **Evento iniciado por** – também conhecido como o "chamador". O endereço de email ou o identificador do Active Directory do usuário que realizou a operação.

>[!NOTE]
>Você deve especificar pelo menos dois dos critérios anteriores em seu alerta, sendo que um deve ser a categoria. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos Logs de Atividade.
>
>

Quando um alerta do Log de Atividades torna-se ativado, ele usa um Grupo de Ações para gerar ações ou notificações. Um Grupo de Ações é um conjunto reutilizável de destinatários da notificação (endereços de email, URLs de webhook ou números de telefone para SMS) que pode ser consultado por vários alertas a fim de centralizar e agrupar seus canais de notificação. Você pode usar um Grupo de Ações existente em seu alerta do Log de Atividades, ou criar um novo Grupo de Ações durante a definição do alerta de seu Log de Atividades. Saiba mais sobre [Grupos de Ação aqui](monitoring-action-groups.md)

Saiba mais sobre Alertas do Log de Atividades para notificação de integridade do serviço clicando [aqui](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Criar um alerta em um evento do log de atividades com um novo grupo de ação com o Portal do Azure
1.  No [Portal](https://portal.azure.com), navegue até a folha **Monitor**.

    ![Monitoramento](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Clique na opção **Monitor** para abrir a folha Monitor. Ela abre primeiro na folha **Log de atividades**.

3.  Agora, clique na folha **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Selecione o comando **Adicionar alerta do log de atividades** e preencha os campos.

5.  **Dê um nome** ao alerta do log de atividades e escolha uma **Descrição**.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  A **Assinatura** selecionada é aquela na qual o alerta do Log de Atividades será salvo. Ela é preenchida automaticamente com a assinatura que você está usando no momento. Essa é a assinatura na qual o recurso de alerta é implantado e da qual monitora os eventos do Log de Atividades.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  Escolha o **Grupo de Recursos** no qual esse recurso de alerta será criado. Observe que esse não é o grupo de recursos que será monitorado *pelo* alerta, mas sim o grupo de recursos no qual o *próprio recurso de alerta será implantado.*

8.  Como opção, selecione uma **Categoria de Eventos**, o que modifica o conjunto de filtros adicionais mostrado. Para eventos Administrativos, isso inclui opções para filtrar por **Grupo de Recursos**, **Recurso**, **Tipo de Recurso**, **Nome da Operação**, **Nível**, **Status** e **Evento iniciado por** para identificar quais eventos esse alerta deve monitorar.

>[!NOTE]
>Você deve especificar pelo menos um dos critérios anteriores em seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos Logs de Atividade.
>
>

9.  Crie um **Novo** Grupo de Ações dando a ele um **Nome** e um **Nome Curto**; o nome curto será referenciado nas notificações enviadas quando esse alerta for ativado.

10. Em seguida, defina uma lista de ações fornecendo os seguintes dados da ação

    a. **Nome:** nome, alias ou identificador da ação.

    b. **Tipo de ação:** escolha o tipo de ação: Webhook, Email ou SMS

    c. **Detalhes:** de acordo com o tipo de ação escolhido, forneça um número de telefone, endereço de email ou URI de webhook.

11. Selecione **OK** ao concluir a criação do alerta.

O alerta demorará alguns minutos para ser totalmente propagado e depois ficará ativo e disparará quando novos eventos corresponderem aos critérios do alerta.

[Confira este documento para obter detalhes sobre o esquema de webhook dos alertas do Log de Atividades](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Criar um alerta em um evento do Log de Atividades para um grupo de ação existente com o Portal do Azure
1.  No [Portal](https://portal.azure.com), navegue até a folha **Monitor**.

    ![Monitoramento](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  Clique na opção **Monitor** para abrir a folha Monitor. Ela abre primeiro na seção **Log de atividades** .

3.  Agora, clique na seção **Alertas**.

    ![Alertas](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  Selecione o comando **Adicionar alerta do log de atividades** e preencha os campos.

5.  **Dê um nome** ao alerta do Log de Atividades e escolha uma **Descrição**. Ela aparece nas notificações enviadas quando esse alerta é acionado.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  A **Assinatura** selecionada é aquela na qual o alerta do Log de Atividades será salvo. Ela é preenchida automaticamente com a assinatura que você está usando no momento. Essa é a assinatura na qual o recurso de alerta é implantado e da qual monitora os eventos do Log de Atividades.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  Escolha o **Grupo de Recursos** no qual esse recurso de alerta será criado. Observe que esse não é o grupo de recursos que será monitorado *pelo* alerta, mas sim o grupo de recursos no qual o *próprio recurso de alerta será implantado.*

8.  Como opção, selecione uma **Categoria de Eventos**, o que modifica o conjunto de filtros adicionais mostrado. Para eventos Administrativos, isso inclui opções para filtrar por **Grupo de Recursos**, **Recurso**, **Tipo de Recurso**, **Nome da Operação**, **Nível**, **Status** e **Evento iniciado por** para identificar quais eventos esse alerta deve monitorar.

9.  Escolha **Notificar Por** um **Grupo de ação existente**. Selecione um Grupo de Ações existente na lista.

10. Selecione **OK** ao concluir a criação do alerta.

O alerta demorará alguns minutos para ser totalmente propagado e depois ficará ativo e disparará quando novos eventos corresponderem aos critérios do alerta.

## <a name="managing-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção Alertas da folha Monitor. Selecione o alerta que você deseja gerenciar e você poderá:
* **Editá-lo**.
* **Excluí-lo**.
* **Desabilitá-lo** ou **Habilitá-lo** se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
- Obtenha uma [visão geral dos alertas](monitoring-overview-alerts.md)
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md)
- Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
- Saiba mais sobre [grupos de ação](monitoring-action-groups.md)  
- Saiba mais sobre as [Notificações de integridade do serviço](monitoring-service-notifications.md)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

