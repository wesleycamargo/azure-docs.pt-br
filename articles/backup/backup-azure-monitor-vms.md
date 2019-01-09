---
title: Monitorar alertas de backup para máquinas virtuais do Azure
description: Monitore eventos e alertas de trabalhos de backup de máquina virtual do Azure. Envie um email com base em alertas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: raynew
ms.openlocfilehash: a7f09341c1362850409a940810a4e2dd20aa7f74
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745033"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorar alertas para os backups das máquinas virtuais do Azure

Os alertas são respostas do serviço informando que um limite do evento foi atingido ou ultrapassado. Saber quando os problemas iniciam pode ser essencial para manter baixos os custos do negócio. Os alertas normalmente não ocorrem em um agendamento e é útil saber assim que possível após sua ocorrência. Por exemplo, quando um trabalho de backup ou de restauração falha, um alerta ocorre em até cinco minutos após a falha. No painel do cofre, o bloco Alertas de Backup exibe os eventos nos níveis Crítico e Aviso. Nas configurações Alertas de Backup, você pode exibir todos os eventos. Mas o que fazer se um alerta ocorrer quando você estiver trabalhando em um problema separado? Se você não sabe quando o alerta ocorre, pode ser uma inconveniência secundária ou pode comprometer os dados. Para verificar se as pessoas corretas estão cientes de um alerta, quando ele ocorre, configure o serviço para enviar notificações de alerta por email. Para obter detalhes sobre como configurar as notificações por email, confira [Configurar notificações](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Como localizar informações sobre os alertas?

Para exibir informações sobre o evento que gerou um alerta, você deve abrir a seção Alertas de Backup. Há duas maneiras de abrir a seção Alertas de Backup: no bloco Alertas de Backup no painel do cofre ou na seção Alertas e Eventos.

Para abrir a folha Alertas de Backup no bloco Alertas de Backup:

* No bloco **Alertas de Backup** no painel do cofre, clique em **Crítico** ou **Aviso** para exibir os eventos operacionais desse nível de gravidade.

    ![Bloco Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir a seção Alertas de Backup na seção Alertas e Eventos:

1. No painel do cofre, clique em **Todas as Configurações**. ![Botão Todas as Configurações](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na folha **Configurações**, clique em **Alertas e Eventos**. ![Botão Alertas e Eventos](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na folha **Alertas e Eventos**, clique em **Alertas de Backup**. ![Botão Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts.png)

    A seção **Alertas de Backup** é aberta e exibe os alertas filtrados.

    ![Bloco Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para exibir informações detalhadas sobre um evento específico, na lista de eventos, clique no alerta para abrir sua seção **Detalhes**.

    ![Detalhe do Evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar os atributos exibidos na lista, confira [Exibir atributos adicionais do evento](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurar notificações

 Você pode configurar o serviço para enviar notificações por email para os alertas que ocorreram durante a última hora ou quando ocorrem determinados tipos de eventos.

Para configurar as notificações por email para alertas

1. No menu Alertas de Backup, clique em **Configurar notificações**

    ![Menu Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    A seção Configurar notificações é aberta.

    ![Folha Configurar notificações](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Na seção Configurar notificações, para as Notificações por email, clique em **Ativar**.

    As caixas de diálogo Destinatários e Gravidade têm uma estrela ao lado porque essa informação é necessária. Forneça pelo menos um endereço de email e selecione pelo menos uma Gravidade.
3. Na caixa de diálogo **Destinatários (Email)** , digite os endereços de email para quem recebe as notificações. Use o formato: username@domainname.com. Separe os vários endereços de email com um ponto e vírgula (;).
4. Na área **Notificar**, escolha **Por Alerta** para enviar uma notificação quando ocorrer o alerta especificado ou **Consumir por Hora** para enviar um resumo da última hora.
5. Na caixa de diálogo **Gravidade** , escolha um ou mais níveis que você deseja para disparar a notificação por email.
6. Clique em **Salvar**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Quais tipos de alertas estão disponíveis para o backup da VM IaaS do Azure

   | Nível de alerta | Alertas enviados |
   | --- | --- |
   | Crítico | para falha de Backup, falha de recuperação |
   | Aviso | para trabalhos de Backup bem-sucedidos com avisos (por exemplo: alguns gravadores falharam ao criar um instantâneo) |
   | Informativo | Atualmente, alertas informativos não estão disponíveis para o backup de VM do Azure |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Situações em que o email não é enviado, mesmo se as notificações estiverem configuradas

Há situações em que um alerta não é enviado, mesmo que as notificações tenham sido corretamente configuradas. Nas situações a seguir, notificações por email não serão enviadas para evitar ruídos de alerta:

* Se as notificações forem configuradas como Resumo de Hora em Hora e se um alerta for gerado e resolvido em uma hora.
* O Trabalho é cancelado.
* Um trabalho de backup é disparado e falha e outro trabalho de backup está em andamento.
* Um trabalho de backup agendado para uma VM habilitada para o Resource Manager, mas a VM não existe mais.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Usar Logs de atividade para obter notificações de backups bem-sucedidos

> [!NOTE]
> Mudamos para um novo modelo de bombeamento logs de atividade do Backup do Azure em cofres dos Serviços de Recuperação. Infelizmente, isso afetou a geração de logs de atividades em Nuvens Soberanas do Azure. Se os usuários da Nuvem Soberana do Azure tiverem criado/configurado alertas a partir de logs de atividade por meio do Azure Monitor, conforme mencionado aqui, eles não seriam disparados. Nesse caso, recomendaríamos a esses usuários que usassem as configurações de diagnóstico e o espaço de trabalho de LA, ou a [Solução de relatório do PowerBI](backup-azure-configure-reports.md) para obter as informações relevantes. Além disso, em todas as regiões públicas do Azure, se um usuário estiver coletando logs de atividade dos Serviços de Recuperação em um espaço de trabalho de Análise de log, conforme mencionado [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), esses logs também não seriam exibidos.

Se você quiser receber uma notificação após a conclusão bem-sucedida dos backups, use os alertas criados nos [logs de atividade](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) do cofre.

### <a name="login-into-azure-portal"></a>Fazer logon no Portal do Azure

Faça logon no Portal do Azure e acesse o cofre relevante dos Serviços de Recuperação do Azure e clique na seção "Log de atividades" nas propriedades.

### <a name="identify-appropriate-log"></a>Identificar o log apropriado

Aplique os filtros mostrados na figura a seguir para verificar se você está recebendo os logs de atividade de backups bem-sucedidos. Altere o intervalo de tempo adequadamente para exibir os registros.

![Logs de atividade](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Clique no segmento "JSON" para obter mais detalhes e exiba-os copiando e colando-os em um editor de texto. Isso deve exibir os detalhes do cofre e o item que disparou o log de atividades, ou seja, o item de backup.

Depois, clique em "Adicionar alerta do log de atividade" para gerar alertas para todos esses logs.

### <a name="add-activity-log-alert"></a>Adicionar alerta do Log de atividades

Clicar em "Adicionar alerta do log de atividades" mostrará uma tela, conforme exibido abaixo

![Alerta do log de atividades](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) A assinatura e o grupo de recursos são usados para armazenar o alerta. Os critérios serão preenchidos previamente. Verifique se que todos os valores são relevantes às suas necessidades.

Para backups bem-sucedidos, o "Nível" é marcado como "Informativo" e o Status como "Bem-sucedido".

Se você selecionar um "recurso" acima, o alerta será gerado quando os logs de atividade forem registrados para esse recurso ou cofre. Se você quiser que a regra seja aplicável a todos os cofres, deixe o "recurso" vazio.

### <a name="define-action-on-alert-firing"></a>Definir ação ao disparar o alerta

Use o "grupo de ações" para definir a ação ao gerar um alerta. Você pode clicar em "Tipo de ação" para saber mais sobre as ações disponíveis, como email/SMS/Integração com ITSM etc.

![Grupo de ações do log de atividades](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Depois de clicar em OK, um alerta do log de atividades será gerado, e os logs de atividade subsequentes registrados para backups bem-sucedidos dispararão a ação conforme definido no grupo de ações.

### <a name="limitations-on-alerts"></a>Limitações sobre alertas

Os alertas baseados em eventos estão sujeitos às seguintes limitações:

1. Os alertas são disparados em todas as máquinas virtuais no cofre dos Serviços de Recuperação. Não é possível personalizar o alerta para um subconjunto de máquinas virtuais em um cofre dos Serviços de Recuperação.
2. Os alertas são enviados de “alerts-noreply@mail.windowsazure.com”. No momento, você não pode modificar o remetente do email.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como recriar uma máquina virtual a partir de um ponto de recuperação, verifique [Restaurar VMs do Azure](backup-azure-arm-restore-vms.md).

Se você precisar de mais informações sobre como proteger as máquinas virtuais, consulte [Apresentação preliminar: Backup de VMs no cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

Saiba mais sobre as tarefas de gerenciamento para backups da VM no artigo [Gerenciar backups da máquina virtual do Azure](backup-azure-manage-vms.md).
