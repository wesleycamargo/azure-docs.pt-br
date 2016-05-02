
<properties
	pageTitle="Restaurar máquinas virtuais do backup usando o portal do Azure | Microsoft Azure"
	description="Restaurar uma máquina virtual do Azure por um ponto de recuperação usando o portal do Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="restaurar o backup; como restaurar; ponto de recuperação;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="trinadhk; jimpark;"/>


# Usar o portal do Azure para restaurar máquinas virtuais

> [AZURE.SELECTOR]
- [Restaurar VMs no portal Clássico](backup-azure-restore-vms.md)
- [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)


Você pode proteger seus dados com o serviço de Backup tirando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados no cofre de serviços de recuperação. Se ou quando é necessário reparar ou recriar uma VM, você pode restaurar a máquina virtual de qualquer um dos pontos de recuperação salvos. Quando você restaura um ponto de recuperação, você retorna ou reverte a VM ao estado quando o ponto de recuperação foi feito. Esse artigo explica como restaurar uma VM.

> [AZURE.NOTE] O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo fornece informações e procedimentos para a restauração de VMs implantadas usando o modelo do Gerenciador de Recursos.



## Restaurar um ponto de recuperação

1. Entre no [portal do Azure](http://ms.portal.azure.com/)

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

5. Na lista, selecione uma VM para abrir o painel.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault-selected.png)

    O painel da VM abre para a área de Monitoramento, que contém o bloco de pontos de recuperação.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. No menu do painel da VM, clique em **Restaurar**

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    A folha Restaurar é aberta.

    ![restaurar folha](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. Na folha **Restaurar**, clique em **ponto de Recuperação** para abrir a folha **Selecionar ponto de Recuperação**.

    ![restaurar folha](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Por padrão, a caixa de diálogo exibe todos os pontos de recuperação dos últimos 30 dias. Edite as listas **Período**, **Ano** e **consistência de ponto de Restauração** como desejar. Para obter mais informações sobre cada tipo de ponto de restauração, consulte a explicação de [consistência de Dados](./backup-azure-vms-introduction.md#data-consistency).
    - **Período** quer dizer os meses do ano ou os últimos 30 dias.
    - **Ano** é o ano específico.
    - **Consistência de ponto de Restauração** dessa lista, escolha:
        - Pontos de restauração consistentes de falha,
        - Pontos de restauração consistentes do aplicativo,
        - Pontos de restauração consistentes de sistema de arquivos
        - Todos os pontos de restauração.  

8. Escolha um ponto de Recuperação e clique em **OK**.

    ![escolher ponto de recuperação](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    A folha **Restaurar** mostra o ponto de Recuperação definido.

    ![o ponto de recuperação está definido](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. Na folha **Restaurar**, clique em **configuração de Recuperação** para abrir sua folha.

    ![o assistente de configuração de recuperação está definido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## Escolhendo uma configuração de recuperação da VM

Agora que você selecionou o ponto de recuperação, escolha uma conta de armazenamento e o método para configurar a sua VM de recuperação. As opções para configurar a VM de recuperação são o uso do: portal do Azure ou PowerShell. Ao escolher uma conta de armazenamento, você deve escolher contas que compartilham o mesmo local que o cofre de Serviços de Recuperação. Não há suporte para as contas de armazenamento com Zona redundante. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre de Serviços de Recuperação, você deverá criar um antes de iniciar a operação de restauração.

1. Se você ainda não estiver nesse item, vá até a folha **Restaurar**. Certifique-se de um **ponto de Recuperação** tenha sido selecionado e clique em **configuração de Recuperação** para abrir a folha **configuração de Recuperação**.

    ![o assistente de configuração de recuperação está definido](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. Na folha **configuração de Recuperação**, clique em **conta de Armazenamento** para abrir a lista de contas de armazenamento no mesmo local que o cofre de Serviços de Recuperação.

3. Na lista, escolha uma conta de armazenamento e clique em OK.

    ![lista de contas de armazenamento](./media/backup-azure-arm-restore-vms/list-of-storage-accounts.png)

    Na folha **configuração de Recuperação** a conta de armazenamento escolhida aparece no diálogo **conta de Armazenamento**.

    ![escolher contas de armazenamento](./media/backup-azure-arm-restore-vms/selected-storage-account.png)

4. Você pode configurar um ARM básico ou VM Clássica ao restaurar uma VM no portal. Se você desejar restaurar uma configuração complexa - como uma das opções na lista a seguir, você deverá usar o PowerShell para configurar a VM do disco. Se você não estiver configurando uma configuração complexa, pule para a etapa 5.

    ![lista de configurações complexas de VM](./media/backup-azure-arm-restore-vms/complex-vm-configurations.png)

    Se a configuração de restauração *for* considerada complexa, na folha **Configuração de Recuperação**, clique em **OK**. A folha **configuração de Recuperação** fecha e uma marca de seleção aparece ao lado da configuração de Recuperação. Clique em **Restaurar** para iniciar o trabalho de restauração de disco. Não continue para a etapa 5; em vez disso, vá para [Restaurar uma VM com configurações de rede especiais](#restoring-vms-with-special-network-configurations).

    ![Restauração configurada](./media/backup-azure-arm-restore-vms/restore-configured.png)

5. Na folha **configuração de Recuperação**, clique em **configuração de máquina virtual** para abrir a folha **Criar VM**.

    ![Abrir folha Criar VM](./media/backup-azure-arm-restore-vms/recovery-configuration-create-vm.png)

6. Na folha **Criar VM**, insira ou selecione valores para cada um dos seguintes campos:

    - **Nome da máquina virtual** - Forneça um nome para a VM. O nome deve ser exclusivo para o grupo de recursos (para uma VM ARM) ou o serviço de nuvem (para uma VM Clássica). Se você estiver substituindo uma VM existente pelo mesmo nome, primeiro exclua a VM existente e os discos de dados e, em seguida, restaure os dados do Backup do Azure.
    - **Grupo de recursos** – Use um grupo de recursos existente ou crie um novo. Se você estiver restaurando uma VM Clássica, use este campo para especificar o nome de um novo serviço de nuvem. Ao criar um novo grupo de recursos/serviço de nuvem, o nome deve ser globalmente exclusivo. Normalmente, o nome do serviço de nuvem está associado a uma URL para o público, por exemplo: [cloudservice].cloudapp.net. Se você tentar usar um nome para o serviço de nuvem/grupo de recursos de nuvem que já tenha sido usado, o Azure atribuirá ao serviço de nuvem/grupo de recursos o mesmo nome que a VM. O Azure exibe serviços de nuvem/grupos de recursos e VMs não associadas a nenhum grupo de afinidades. Para obter mais informações, consulte [Como migrar de Grupos de Afinidades para uma VNet (Rede Virtual Regional)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
    - **Rede virtual** - Selecione a VNET (Rede Virtual) ao criar a VM. O campo fornece todas as redes virtuais associadas à assinatura.

    > [AZURE.NOTE] Você deve selecionar uma rede virtual ao restaurar uma VM baseada em ARM. Uma rede virtual é opcional para uma VM Clássica.

    - **Sub-rede** - Se a rede virtual tiver sub-redes, a primeira sub-rede será selecionada por padrão. Se houver sub-redes adicionais, selecione a sub-rede desejada.

7. Na folha **Criar VM**, clique em **OK** para concluir a configuração.

    A folha **Criar VM** é fechada e a folha **configuração de Recuperação** mostra o nome da nova VM.

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/recovery-configuration-complete.png)

8. Na folha **configuração de Recuperação**, clique em **OK** para finalizar a configuração de restauração.

9. Na folha **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## Acompanhar a operação de restauração

Depois de disparar a operação de restauração, o serviço de Backup cria um trabalho para acompanhar a operação de restauração. O serviço de Backup também cria e exibe temporariamente a notificação.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-triggered.png)

Se você não vir a notificação, você sempre poderá clicar no ícone de Notificações para exibir as notificações.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para exibir a operação durante o processamento ou para exibir quando for concluída, abra a lista de trabalhos de Backup.

1. No menu do Azure, clique em **Procurar** e, na lista de serviços, digite **Serviços de Recuperação**. A lista de serviços é ajustada de acordo com o que você digitar. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

    ![Abrir cofre de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. Na lista, selecione o cofre associado à VM que restaurada. Ao clicar no cofre, o painel é aberto.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/select-vault-open-vault-jobs.png)

3. No painel do cofre, no bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure** para exibir os trabalhos associados ao cofre.

    ![painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    A folha **Trabalhos de Backup** é aberta e exibe a lista de trabalhos.

    ![lista de VMs no cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)




## Restaurando VMs com configurações de rede especiais
É possível fazer backup e restaurar as VMs com as seguintes configurações de rede especiais. No entanto, essas configurações exigem alguma consideração especial ao percorrer o processo de restauração.

- VMs no balanceador de carga (interno e externo)
- VMs com vários IPs reservados
- VMs com vários NICs

>[AZURE.IMPORTANT] Ao criar a configuração de rede especial para VMs, você deve usar o PowerShell para restaurar pelo disco.


Para recriar completamente as máquinas virtuais após restaurar para o disco, siga essas etapas:

1. Restaurar os discos de um cofre dos serviços de recuperação usando o [PowerShell do Backup do Azure](../backup-azure-vms-automation.md#restore-an-azure-vm)

2. Crie a configuração da VM necessária para o balanceador de carga/múltiplos NICs, múltiplos IPs reservados usando os cmdlets do PowerShell e use-a para criar a VM com a configuração desejada.
	- Criar a VM no serviço de nuvem com o [balanceador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
	- Criar a máquina virtual para se conectar ao [balanceador de carga voltado para a Internet](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)
	- Criar uma VM [com várias NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
	- Criar VMs com [vários IPs reservados](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## Próximas etapas
Agora que você pode restaurar suas VMs, consulte o artigo de solução de problemas para obter informações sobre erros comuns com VMs. Além disso, confira o artigo sobre como gerenciar tarefas com suas VMs.

- [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
- [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->