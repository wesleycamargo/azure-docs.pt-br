
---
title: Restaurar máquinas virtuais do backup usando o portal do Azure | Microsoft Docs
description: Restaurar uma máquina virtual do Azure por um ponto de recuperação usando o portal do Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: cfreeman
editor: ''
keywords: restaurar o backup; como restaurar; ponto de recuperação;

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: trinadhk; jimpark;

---
# Usar o portal do Azure para restaurar máquinas virtuais
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal Clássico](backup-azure-restore-vms.md)
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
> 
> 

Proteja seus dados criando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados no cofre de serviços de recuperação. Se ou quando é necessário reparar ou recriar uma VM, você pode restaurar a máquina virtual de qualquer um dos pontos de recuperação salvos. Quando você restaura um ponto de recuperação, você retorna ou reverte a VM ao estado quando o ponto de recuperação foi feito. Esse artigo explica como restaurar uma VM.

> [!NOTE]
> O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo fornece informações e procedimentos para a restauração de VMs implantadas usando o modelo do Gerenciador de Recursos.
> 
> 

## Restaurar um ponto de recuperação
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

## Escolhendo uma configuração de restauração de VM
Agora que você selecionou o ponto de restauração, escolha uma configuração para a sua VM de restauração. As opções para configurar a VM restaurada são usar o portal do Azure ou o PowerShell.

> [!NOTE]
> O portal fornece uma opção de criação rápida para a VM restaurada. Se você quiser personalizar a configuração de VM da VM prestes a ser restaurada, use o PowerShell para restaurar os discos com backup e anexá-los à sua opção de configuração da VM. Confira [Restaurar uma VM com configurações de rede especiais](#restoring-vms-with-special-network-configurations).
> 
> 

1. Se você ainda não estiver nesse item, vá até a folha **Restaurar**. Certifique-se de que um **Ponto de restauração** foi selecionado e clique em **Restaurar configuração** para abrir a folha **Configuração de recuperação**.
   
    ![o assistente de configuração de recuperação está definido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)
2. Na folha **Restaurar configuração**, insira ou selecione valores para cada um dos seguintes campos:
   
   * **Nome da máquina virtual** - Forneça um nome para a VM. O nome deve ser exclusivo para o grupo de recursos (para uma VM implantada pelo Gerenciador de Recursos) ou serviço de nuvem (para uma VM clássica). Não é possível substituir a máquina virtual se ela já existe na assinatura.
   * **Grupo de recursos** – use um grupo de recursos existente ou crie um novo. Se você estiver restaurando uma VM Clássica, use este campo para especificar o nome de um novo serviço de nuvem. Se você cria umr novo grupo de recursos/serviço de nuvem, o nome deverá ser exclusivo. Normalmente, o nome do serviço de nuvem está associado a uma URL para o público, por exemplo: [cloudservice].cloudapp.net. Se você tentar usar um nome para o serviço de nuvem/grupo de recursos de nuvem que já tenha sido usado, o Azure atribuirá ao serviço de nuvem/grupo de recursos o mesmo nome que a VM. O Azure exibe serviços de nuvem/grupos de recursos e VMs não associadas a nenhum grupo de afinidades. Para saber mais, confira [Como migrar de Grupos de Afinidades para uma Rede Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
   * **Rede virtual** - selecione a rede virtual (VNET) ao criar a VM. O campo fornece todas as redes virtuais associadas à assinatura. O grupo de recursos da VM é exibido entre parênteses.
   * **Sub-rede** - se a rede virtual tiver sub-redes, a primeira sub-rede será selecionada por padrão. Se houver sub-redes adicionais, selecione a sub-rede desejada.
   * **Conta de armazenamento** - esse menu lista as contas de armazenamento no mesmo local que o cofre de Serviços de Recuperação. Ao escolher uma conta de armazenamento, selecione uma conta que compartilhe o mesmo local que o cofre de Serviços de Recuperação. Não há suporte para as contas de armazenamento com Zona redundante. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre de Serviços de Recuperação, você deverá criar um antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é mencionado entre parênteses.
     
     > [!NOTE]
     > Se você estiver restaurando uma VM implantada pelo Gerenciador de Recursos, deverá identificar uma VNET (rede virtual). Uma VNET (rede virtual) é opcional para uma VM Clássica.
     > 
     > 
3. Na folha **Restaurar configuração** folha, clique em **OK** para finalizar a configuração da restauração.
4. Na folha **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.
   
    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## Acompanhar a operação de restauração
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

## Restaurando VMs com configurações de rede especiais
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
   * Criar a máquina virtual para se conectar ao [balanceador de carga voltado para a Internet](https://azure.microsoft.com/pt-BR/documentation/articles/load-balancer-internet-getstarted/)
   * Criar uma VM [com várias NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Criar VMs com [vários IPs reservados](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## Próximas etapas
Agora que você pode restaurar suas VMs, consulte o artigo de solução de problemas para obter informações sobre erros comuns com VMs. Além disso, confira o artigo sobre como gerenciar tarefas com suas VMs.

* [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0810_2016-->