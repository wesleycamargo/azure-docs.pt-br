---
title: "Backup do Azure: Restaurar máquinas virtuais usando o portal do Azure | Microsoft Docs"
description: "Restaurar uma máquina virtual do Azure por um ponto de recuperação usando o portal do Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restaurar o backup; como restaurar; ponto de recuperação;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/26/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: d7a2b9c13b2c3372ba2e83f726c7bf5cc7e98c02
ms.openlocfilehash: 6f55bdbb97ead96edf7ca41562b1c4b5a712d6e8
ms.lasthandoff: 02/17/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Usar o portal do Azure para restaurar máquinas virtuais
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal Clássico](backup-azure-restore-vms.md)
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
>
>

Proteja seus dados criando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados no cofre de serviços de recuperação. Se ou quando é necessário reparar ou recriar uma VM, você pode restaurar a máquina virtual de qualquer um dos pontos de recuperação salvos. Quando você restaura um ponto de recuperação, você retorna ou reverte a VM ao estado quando o ponto de recuperação foi feito. Esse artigo explica como restaurar uma VM.

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece informações e procedimentos para a restauração de VMs implantadas usando o modelo do Gerenciador de Recursos.
>
>

## <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação
1. Entre no [Portal do Azure](http://portal.azure.com/)
2. No menu do Azure, clique em **Procurar** e, na lista de serviços, digite **Serviços de Recuperação**. A lista de serviços é ajustada de acordo com o que você digitar. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

    ![Abrir cofre de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Na lista, selecione o cofre associado à VM que deseja restaurar. Ao clicar no cofre, o painel é aberto.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Agora que você está no painel do cofre. No bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure** para exibir as VMs associadas ao cofre.

    ![painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    A folha **Itens de Backup** é aberta e exibe a lista de máquinas virtuais do Azure.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Na lista, selecione uma VM para abrir o painel. O painel da VM abre na Área de monitoramento, que contém o bloco Pontos de restauração.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. No menu do painel da VM, clique em **Restaurar**

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    A folha Restaurar é aberta.

    ![restaurar folha](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Na folha **Restaurar**, clique em **Ponto de restauração** para abrir a folha **Selecionar Ponto de restauração**.

    ![restaurar folha](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Por padrão, a caixa de diálogo exibe todos os pontos de restauração dos últimos 30 dias. Use o **Filtro** alterar o intervalo de tempo dos pontos de restauração exibidos. Por padrão, são exibidos pontos de restauração de todas as consistências. Modifique o filtro **Todos os Pontos de restauração** para selecionar uma consistência específica de pontos de restauração. Para saber mais sobre cada tipo de ponto de restauração, confira a explicação de [Consistência de dados](backup-azure-vms-introduction.md#data-consistency).  

   * **Consistência de pontos de restauração** nessa lista, escolha:
     * Pontos de restauração consistentes de falha,
     * Pontos de restauração consistentes do aplicativo,
     * Pontos de restauração consistentes de sistema de arquivos
     * Todos os pontos de restauração.  
8. Escolha um Ponto de restauração e clique em **OK**.

    ![escolha o ponto de restauração](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    A folha **Restaurar** mostra que o Ponto de restauração está definido.

    ![ponto de restauração definido](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. Na folha **Restaurar**, **Restaurar configuração** abre automaticamente após o ponto de restauração estar definido.

    ![assistente de configuração de restauração definido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## <a name="choosing-a-vm-restore-configuration"></a>Escolhendo uma configuração de restauração de VM
Agora que você selecionou o ponto de restauração, escolha uma configuração para a sua VM de restauração. As opções para configurar a VM restaurada são usar o portal do Azure ou o PowerShell.

> [!NOTE]
> O portal fornece uma opção de criação rápida para a VM restaurada. Se você personalizar a configuração da VM que será restaurada, use o PowerShell ou o portal para restaurar os discos com backup e use comandos do PowerShell para anexá-los à configuração da VM de sua escolha. Confira [Restaurar uma VM com configurações de rede especiais](#restoring-vms-with-special-network-configurations).
>
>

1. Se você ainda não estiver nesse item, vá até a folha **Restaurar** . Certifique-se de que um **Ponto de restauração** foi selecionado e clique em **Restaurar configuração** para abrir a folha **Configuração de recuperação**.

    ![o assistente de configuração de recuperação está definido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)
2. Na folha **Restaurar configuração**, você tem duas opções:

   * Restaurar a máquina virtual completa
   * Restaurar discos com backup

   ### <a name="restore-full-virtual-machine"></a>Restaurar a máquina virtual completa
   Na folha **Restaurar configuração** , insira ou selecione valores para cada um dos seguintes campos:

   * **Tipo de Restauração** – Criar máquina virtual.
   * **Nome da máquina virtual** - Forneça um nome para a VM. O nome deve ser exclusivo para o grupo de recursos (para uma VM implantada pelo Gerenciador de Recursos) ou serviço de nuvem (para uma VM clássica). Não é possível substituir a máquina virtual se ela já existe na assinatura.
   * **Grupo de recursos** – use um grupo de recursos existente ou crie um novo. Se você estiver restaurando uma VM Clássica, use este campo para especificar o nome de um novo serviço de nuvem. Se você estiver criando um novo grupo de recursos/serviço de nuvem, o nome deverá ser exclusivo globalmente. Normalmente, o nome do serviço de nuvem está associado a uma URL para o público, por exemplo: [cloudservice].cloudapp.net. Se você tentar usar um nome para o serviço de nuvem/grupo de recursos de nuvem que já tenha sido usado, o Azure atribuirá ao serviço de nuvem/grupo de recursos o mesmo nome que a VM. O Azure exibe serviços de nuvem/grupos de recursos e VMs não associadas a nenhum grupo de afinidades. Para saber mais, confira [Como migrar de Grupos de Afinidades para uma Rede Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
   * **Rede virtual** - selecione a rede virtual (VNET) ao criar a VM. O campo fornece todas as redes virtuais associadas à assinatura. O grupo de recursos da VM é exibido entre parênteses.
   * **Sub-rede** - se a rede virtual tiver sub-redes, a primeira sub-rede será selecionada por padrão. Se houver sub-redes adicionais, selecione a sub-rede desejada.
   * **Conta de armazenamento** - esse menu lista as contas de armazenamento no mesmo local que o cofre de Serviços de Recuperação. Ao escolher uma conta de armazenamento, selecione uma conta que compartilhe o mesmo local que o cofre de Serviços de Recuperação. Não há suporte para as contas de armazenamento com Zona redundante. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre de Serviços de Recuperação, você deverá criar um antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é mencionado entre parênteses.

     > [!NOTE]
     > Se você estiver restaurando uma VM implantada pelo Gerenciador de Recursos, deverá identificar uma VNET (rede virtual). Uma VNET (rede virtual) é opcional para uma VM Clássica.
     >
     >

   ### <a name="restore-backed-up-disks"></a>Restaurar discos com backup
   Se quiser personalizar a máquina virtual que você gostaria de criar nos discos com backup, de forma diferente do que está presente na folha de configuração de restauração, selecione **Restaurar discos** como valor para **Tipo de Restauração**. Essa opção solicita uma conta de armazenamento (as mesmas restrições de Restaurar conta de armazenamento de máquina virtual completa se aplicam) para a qual os discos de backup serão copiados. Você pode [usar os discos restaurados para anexar a uma máquina virtual existente](../virtual-machines/virtual-machines-windows-attach-disk-portal.md) ou [criar uma nova máquina virtual de discos restaurados](./backup-azure-vms-automation.md#restore-an-azure-vm).

3. Na folha **Restaurar configuração** folha, clique em **OK** para finalizar a configuração da restauração.
4. Na folha **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração
Depois de disparar a operação de restauração, o serviço de Backup cria um trabalho para acompanhar a operação de restauração. O serviço de Backup também cria e exibe a notificação temporariamente na área de Notificações do portal. Se você não vir a notificação, você sempre poderá clicar no ícone de Notificações para exibir as notificações.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para exibir a operação durante o processamento ou para exibir quando for concluída, abra a lista de trabalhos de Backup.

1. No menu do Azure, clique em **Procurar** e, na lista de serviços, digite **Serviços de Recuperação**. A lista de serviços é ajustada de acordo com o que você digitar. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

    ![Abrir cofre de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Na lista, selecione o cofre associado à VM que restaurada. Ao clicar no cofre, o painel é aberto.
3. No painel do cofre, no bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure** para exibir os trabalhos associados ao cofre.

    ![painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    A folha **Trabalhos de Backup** é aberta e exibe a lista de trabalhos.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)

## <a name="post-restore-steps"></a>Etapas de pós-restauração
* Se você estiver usando uma distribuição do Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/virtual-machines-linux-classic-reset-access.md). Recomendamos o uso de chaves SSH nessas distribuições para evitar a redefinição de senha após a restauração.
* As extensões presentes durante a configuração do backup serão instaladas, mas não serão habilitadas. Se você vir qualquer problema, reinstale as extensões. 
* Se, após a restauração, a VM de backup tiver IP estático, a VM restaurada terá um IP dinâmico para evitar conflitos ao criar uma VM restaurada. Saiba mais sobre como [adicionar um endereço IP estático à VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* A VM restaurada não terá o valor de disponibilidade definido. Recomendamos o uso da opção de discos de restauração e [adição do conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-create-availability-set.md#use-powershell-to-create-an-availability-set) ao criar uma VM do PowerShell usando discos restaurados. 

## <a name="backup-for-restored-vms"></a>Backup de VMs restauradas
Se você tiver restaurado a VM no mesmo Grupo de recursos e com o mesmo nome que o backup original da VM, o backup continuará na VM após a restauração. Se você tiver restaurado a VM para um Grupo de recursos diferente ou especificado um nome diferente para a VM restaurada, ela será tratada como uma nova VM e você precisará configurar o backup para a VM restaurada.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restaurando uma VM durante desastres de data center do Azure
O Backup do Azure permite restaurar backups de VMs em um data center emparelhado caso ocorra um desastre no data center principal onde as VMs estão sendo executadas e você tenha configurado o Cofre de Backup para ser redundante geograficamente. Durante esses cenários, você precisa selecionar uma conta de armazenamento que esteja presente no data center emparelhado e o restante do processo de restauração permanece o mesmo. O Backup do Azure usa o serviço de computação da área geográfica emparelhada para criar a máquina virtual restaurada. Saiba mais sobre [Resiliência do data center do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-vms-with-special-network-configurations"></a>Restaurando VMs com configurações de rede especiais
É possível fazer backup e restaurar as VMs com as seguintes configurações de rede especiais. No entanto, essas configurações exigem alguma consideração especial ao percorrer o processo de restauração.

* VMs no balanceador de carga (interno e externo)
* VMs com vários IPs reservados
* VMs com vários NICs

> [!IMPORTANT]
> Ao criar a configuração de rede especial para VMs, você precisa usar o PowerShell para criar VMs a partir dos discos restaurados.
>
>

Para recriar completamente as máquinas virtuais após restaurar para o disco, siga essas etapas:

1. Restaure os discos de um cofre de serviços de recuperação usando o [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)
2. Crie a configuração da VM necessária para o balanceador de carga/múltiplos NICs, múltiplos IPs reservados usando os cmdlets do PowerShell e use-a para criar a VM com a configuração desejada.

   * Criar a VM no serviço de nuvem com o [balanceador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Criar a VM para se conectar ao [balanceador de carga voltado para a Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Criar uma VM [com várias NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Criar VMs com [vários IPs reservados](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Próximas etapas
Agora que você pode restaurar suas VMs, consulte o artigo de solução de problemas para obter informações sobre erros comuns com VMs. Além disso, confira o artigo sobre como gerenciar tarefas com suas VMs.

* [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)

