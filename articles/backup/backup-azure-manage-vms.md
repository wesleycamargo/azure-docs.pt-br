---
title: Gerenciar e monitorar backups de VM do Azure com o serviço de Backup do Azure
description: Saiba como gerenciar e monitorar backups de VM do Azure com o serviço de Backup do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430056"
---
# <a name="manage-azure-vm-backups"></a>Gerenciar backups de VM do Azure

Este artigo descreve como gerenciar VMs do Azure com backup feito com o [serviço de Backup do Azure](backup-overview.md) e resume informações de alertas de backup disponíveis no painel do portal.


No portal do Azure, o painel do cofre dos Serviços de Recuperação fornece acesso a informações sobre o cofre, inclusive:

* O backup mais recente, que também é o ponto de restauração mais recente.
* A política de backup
* O tamanho total de todos os instantâneos de backup
* O número de VMs habilitadas para backup

Você pode gerenciar backups usando o painel e detalhar até as VMs individuais. O backup do computador começa abrindo o cofre no painel. 

![Exibição completa com controle deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Exibir VMs no painel

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Cofre de Serviços de Recuperação**. 
    ![Criar Cofre dos Serviços de Recuperação; etapa 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Para facilidade de uso, clique no cofre com o botão direito do mouse na lista de cofres > **Fixar no painel**.
3. Abra o painel do cofre. 
    ![Abrir o painel do cofre e a folha de Configurações](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. No bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure**.

    ![Bloco Abrir itens de backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. Na folha **Itens de Backup**, veja o último trabalho de backup de cada item. Neste exemplo, há uma máquina virtual, demovm-markgal, protegida por esse cofre.  

    ![Bloco Itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. No painel de itens do cofre, você pode criar ou modificar políticas de backup, exibir pontos de restauração, executar um backup sob demanda, interromper e retomar a proteção de VMs, excluir pontos de recuperação e executar uma restauração.

    ![Painel dos itens de backup com a folha Configurações](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Gerenciar políticas de backup
1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Todas as Configurações**.

    ![Folha Política de backup](./media/backup-azure-manage-vms/all-settings-button.png)
2. Em **Configurações**, clique em**Política de backup**.
3. No menu **Escolher política de backup** :

   * Para alterar as políticas, selecione uma política diferente e clique em **Salvar**. A nova política será aplicada imediatamente no cofre.
   * Para criar uma política, selecione **Criar Nova**. [Saiba mais](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Backup de máquinas virtuais](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda
Você pode fazer um backup sob demanda de uma VM quando ela estiver configurada para proteção.
- Se o backup inicial está pendente, o backup sob demanda cria uma cópia completa da máquina virtual no cofre dos Serviços de Recuperação.
- Se o backup inicial for concluído, um backup sob demanda enviará apenas as alterações do instantâneo anterior para o cofre dos Serviços de Recuperação. Ou seja, os backups subsequentes serão sempre incrementais.
- O período de retenção para um backup sob demanda é o valor de retenção especificado para o ponto de backup Diário na política. Se nenhum ponto de backup Diário for selecionado, o ponto de backup Semanal será usado.


Para disparar um backup sob demanda:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Fazer backup agora**.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Clique em **Sim** para iniciar o trabalho de backup.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

 
 O trabalho de backup cria um ponto de recuperação. O intervalo de retenção do ponto de recuperação é o mesmo especificado na política associada à máquina virtual. Para acompanhar o progresso do trabalho, no painel do cofre, clique no bloco **Trabalhos de Backup** .  

## <a name="stop-protecting-a-vm"></a>Interromper a proteção de uma VM

Há duas maneiras de interromper a proteção de VMs:

- Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação. Você não poderá restaurar a VM nesse caso.
- Interromper todos os trabalhos de backup futuros, mas deixar os pontos de recuperação. Há um custo associado a deixar os pontos de recuperação no armazenamento. No entanto, a vantagem de deixar os pontos de recuperação é que você pode restaurar a VM se necessário. [Saiba mais](https://azure.microsoft.com/pricing/details/backup/) sobre os detalhes de preços.

Para interromper a proteção para uma máquina virtual:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Interromper backup**.

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png
2. Escolha se deseja reter ou excluir os dados de backup e confirme se necessário. Confirme se necessário e, opcionalmente, forneça um comentário. Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome.

    ![Parar a proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Uma mensagem de notificação permite que você conheça os trabalhos de backup que foram interrompidos.


## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de uma VM

Se você tiver mantido dados de backup quando a VM foi interrompida, poderá retomar a proteção. Se você tiver excluído os dados de backup, não será possível retomar.

Te

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Retomar backup**.

2. Siga as etapas em [Gerenciar políticas de backup](backup-azure-manage-vms.md#manage-backup-policies), para atribuir a política da máquina virtual. Você pode escolher uma política diferente da política com a qual a máquina virtual foi inicialmente protegida.
3. Depois da política de backup ser aplicada à máquina virtual, você vê a mensagem a seguir.

    ![VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Excluir dados de backup

Você pode excluir os dados de backup associados a uma VM durante o trabalho **Interromper backup** ou a qualquer momento depois que o trabalho de backup for concluído.

- Pode até mesmo ser benéfico aguardar dias ou semanas antes de excluir os pontos de recuperação.
- Ao contrário de restaurar os pontos de recuperação, ao excluir os dados do backup, você não pode escolher os pontos de recuperação específicos para excluir. Se você escolher excluir os dados de backup, apagará todos os pontos de recuperação associados ao item.

Este procedimento pressupõe que o trabalho de Backup da VM foi interrompido ou desabilitado.


1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Excluir backup**.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Você deve digitar o nome do item para confirmar que deseja excluir os pontos de recuperação.

    ![Interromper verificação](./media/backup-azure-manage-vms/item-verification-box.png)

4. Para excluir os dados do backup do item atual, clique no ![Botão Parar Backup](./media/backup-azure-manage-vms/delete-button.png)

    Uma mensagem de notificação permite que você conheça os dados do backup que foram excluídos.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre](backup-azure-vms-first-look-arm.md) fazer backup de VMs do Azure usando as configurações da VM.
- [Saiba mais sobre](backup-azure-arm-restore-vms.md) restauração de VMs. 
- [Saiba mais sobre](backup-azure-monitor-vms.md) monitoramento de backups de VM do Azure.
 
