---
title: "Restaurar uma máquina virtual do backup | Microsoft Docs"
description: "Aprenda a restaurar uma máquina virtual do Azure a partir de um ponto de recuperação"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "restaurar o backup; como restaurar; ponto de recuperação;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: trinadhk; jimpark;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 64557a71f30762befe07616c3d274a621f22e235
ms.lasthandoff: 03/27/2017


---
# <a name="restore-virtual-machines-in-azure"></a>Restaurar máquinas virtuais no Azure
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
> * [Restaurar VMs no portal Clássico](backup-azure-restore-vms.md)
> 
> 

Com as etapas a seguir, restaure uma máquina virtual em uma nova VM a partir de backups armazenados no cofre de backup do Azure.

## <a name="restore-workflow"></a>Restaurar o fluxo de trabalho
### <a name="step-1-choose-an-item-to-restore"></a>Etapa 1: Escolha um item para restaurar
1. Navegue até a guia **Itens Protegidos** e selecione a máquina virtual que você deseja restaurar em uma nova VM.
   
    ![Itens protegidos](./media/backup-azure-restore-vms/protected-items.png)
   
    A coluna **Ponto de Recuperação** na página **Itens Protegidos** informará o número de pontos de recuperação para uma máquina virtual. A coluna **Ponto de Recuperação Mais Recente** indica a hora do backup mais recente do qual uma máquina virtual pode ser restaurada.
2. Clique em **Restaurar** para abrir o assistente **Restaurar um Item**.
   
    ![Restaurar um Item](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>Etapa 2: Selecione um ponto de recuperação
1. Na tela **Selecionar um ponto de recuperação** , você pode restaurar do ponto de recuperação mais recente ou de um ponto anterior no tempo. A opção padrão selecionada quando o assistente é aberto é *Ponto de Recuperação Mais Recente*.
   
    ![Selecionar um ponto de recuperação](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Para selecionar um ponto anterior no tempo, escolha a opção **Selecionar Data** na lista suspensa e selecione uma data no controle de calendário, clicando no **ícone de calendário**. No controle, todas as datas que têm pontos de recuperação são preenchidas com um tom cinza claro e podem ser selecionadas pelo usuário.
   
    ![Selecione uma data](./media/backup-azure-restore-vms/select-date.png)
   
    Quando você clica em uma data no controle de calendário, os pontos de recuperação disponíveis nessa data serão mostrados na tabela de pontos de recuperação abaixo. A coluna **Tempo** indica a hora em que o instantâneo foi criado. A coluna **Tipo** exibe a [consistência](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) do ponto de recuperação. O cabeçalho da tabela mostra o número de pontos de recuperação disponíveis nesse dia entre parênteses.
   
    ![Pontos de Recuperação](./media/backup-azure-restore-vms/recovery-points.png)
3. Selecione o ponto de recuperação na tabela **Pontos de Recuperação** e clique na seta Avançar para ir para a próxima tela.

### <a name="step-3-specify-a-destination-location"></a>Etapa 3: Especifique um local de destino
1. Na tela **Selecionar instância de restauração** , especifique os detalhes de onde restaurar a máquina virtual.
   
   * Especifique o nome da máquina virtual: em um determinado serviço de nuvem, o nome da máquina virtual deve ser exclusivo. Não há suporte para substituição de VM existente. 
   * Selecione um serviço de nuvem para a VM: isso é obrigatório para a criação de uma VM. Você pode optar por usar um serviço de nuvem existente ou criar um novo serviço de nuvem.
     
        Qualquer nome de serviço de nuvem escolhido deve ser globalmente exclusivo. Normalmente, o nome do serviço de nuvem é associado a uma URL para o público na forma de [cloudservice].cloudapp.net. O Azure não permitirá que você crie um novo serviço de nuvem se o nome já tiver sido usado. Se você optar por criar um novo serviço de nuvem, ele receberá o mesmo nome que a máquina virtual; nesse caso, o nome da VM escolhido deve ser exclusivo o suficiente para ser aplicado ao serviço de nuvem associado.
     
        Só exibimos serviços de nuvem e redes virtuais que não estejam associados a nenhum grupo de afinidade nos detalhes da instância de restauração. [Saiba mais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Selecione uma conta de armazenamento para a VM: isso é obrigatório para a criação de uma VM. Você pode selecionar entre contas de armazenamento existentes na mesma região que o cofre de backup do Azure. Não há suporte para contas de armazenamento com redundância de zona ou do tipo de armazenamento Premium.
   
    Se não houver nenhuma conta de armazenamento com uma configuração com suporte, crie uma conta de armazenamento com uma configuração com suporte antes de iniciar a operação de restauração.
   
    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-sa.png)
3. Selecione uma rede virtual: a rede virtual (VNET) para a máquina virtual deve ser selecionada no momento da criação da VM. A interface do usuário de restauração mostra todas as VNETs nessa assinatura que podem ser usadas. Não é obrigatório selecionar uma VNET para a VM restaurada – você poderá se conectar à máquina virtual restaurada pela Internet mesmo que a VNET não esteja aplicada.
   
    Se o serviço de nuvem selecionado estiver associado a uma rede virtual, você não poderá alterar a rede virtual.
   
    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Selecione uma sub-rede: no caso de a VNET ter sub-redes, por padrão, a primeira sub-rede será selecionada. Selecione a sub-rede de sua escolha entre as opções do menu suspenso. Para obter detalhes de sub-rede, acesse a extensão Redes na [home page do portal](https://manage.windowsazure.com/), vá para **Redes Virtuais** , selecione a rede virtual e faça uma busca detalhada em Configurar para ver os detalhes de sub-rede.
   
    ![Selecione uma sub-rede](./media/backup-azure-restore-vms/select-subnet.png)
5. Clique no ícone **Enviar** no assistente para enviar os detalhes e criar um trabalho de restauração.

## <a name="track-the-restore-operation"></a>Rastrear a operação de restauração
Depois que você inserir todas as informações no assistente de restauração e enviar, o Backup do Azure tentará criar um trabalho para rastrear a operação de restauração.

![Criando um trabalho de restauração](./media/backup-azure-restore-vms/create-restore-job.png)

Se a criação do trabalho for bem-sucedida, você verá uma notificação do sistema indicando que o trabalho foi criado. Você pode obter mais detalhes clicando no botão **Exibir Trabalho**, que o levará para a guia **Trabalhos**.

![Trabalho de restauração criado](./media/backup-azure-restore-vms/restore-job-created.png)

Quando a operação de restauração for concluída, ela será marcada como concluída na guia **Trabalhos** .

![Trabalho de restauração concluído](./media/backup-azure-restore-vms/restore-job-complete.png)

Depois de restaurar a máquina virtual, talvez seja necessário reinstalar as extensões existentes na VM original e [modificar os pontos de extremidade](../virtual-machines/windows/classic/setup-endpoints.md) para a máquina virtual no portal do Azure.

## <a name="post-restore-steps"></a>Etapas de pós-restauração
Se você estiver usando uma distribuição do Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/linux/classic/reset-access.md). Recomendamos o uso de chaves SSH nessas distribuições para evitar a redefinição de senha após a restauração. 

## <a name="backup-for-restored-vms"></a>Backup de VMs restauradas
Se você tiver restaurado a VM no mesmo serviço de nuvem com o mesmo nome que o backup original da VM, o backup continuará na VM após a restauração. Se você tiver restaurado a VM em um serviço de nuvem diferente ou especificado um nome diferente para a VM restaurada, isso será tratado como uma nova VM e você precisará configurar o backup para a VM restaurada.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restaurando uma máquina virtual durante desastres de data center do Azure
O Backup do Azure permite restaurar backups de VMs em um data center emparelhado caso ocorra um desastre no data center principal onde as VMs estão sendo executadas e você tenha configurado o Cofre de Backup para ser redundante geograficamente. Durante esses cenários, você precisa selecionar uma conta de armazenamento que está presente no data center emparelhado e o restante do processo de restauração permanece o mesmo. O Backup do Azure usa o serviço de computação da área geográfica emparelhada para criar a máquina virtual restaurada. Saiba mais sobre [Resiliência do data center do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Restaurando VMs do controlador de domínio
O backup de máquinas virtuais de controlador de domínio (DC) é um cenário com suporte no Backup do Azure. No entanto, deve-se tomar algum cuidado durante o processo de restauração. A experiência de restauração é muito diferente para VMs do controlador de domínio em uma configuração de DC único em comparação a VMs em uma configuração de vários DCs.

### <a name="single-dc"></a>DC único
A VM pode ser restaurada (como qualquer outra VM) do portal do Azure ou usando o PowerShell.

### <a name="multiple-dcs"></a>Vários DCs
Quando você tiver um ambiente com vários DCs, os controladores de domínio terão a sua própria maneira de manter os dados sincronizados. Quando um ponto de backup mais antigo for restaurado *sem as precauções adequadas*, o processo de reversão do USN poderá causar estragos em um ambiente de vários DCs. A maneira correta de recuperar tal VM é inicializá-la em modo DSRM.

O desafio surge porque o modo DSRM não está presente no Azure. Então, para restaurar essa VM, você não poderá usar o portal do Azure. O único mecanismo de restauração com suporte é a restauração baseada em disco usando o PowerShell.

> [!WARNING]
> Para VMs do controlador de domínio em um ambiente com vários DCs, não use o portal do Azure para a restauração! Somente a restauração baseada no PowerShell tem suporte
> 
> 

Leia mais sobre o [problema de reversão de USN](https://technet.microsoft.com/library/dd363553) e as estratégias sugeridas para corrigi-lo.

## <a name="restoring-vms-with-special-network-configurations"></a>Restaurando VMs com configurações de rede especiais
O Backup do Azure oferece suporte ao backup das seguintes configurações de rede especiais de máquinas virtuais.

* VMs no balanceador de carga (interno e externo)
* VMs com vários IPs reservados
* VMs com vários NICs

Essas configurações determinam as considerações a seguir ao restaurá-las.

> [!TIP]
> Use o fluxo de restauração baseado no PowerShell para recriar a configuração de rede especial das VMs após a restauração.
> 
> 

### <a name="restoring-from-the-ui"></a>Restauração a partir da interface do usuário:
Ao restaurar da interface do usuário, **sempre escolha um novo serviço de nuvem**. Observe que como o portal usa apenas os parâmetros obrigatórios durante o fluxo de restauração, as VMs restauradas usando a interface do usuário perderão a configuração de rede especial que possuem. Em outras palavras, as VMs restauradas serão VMs normais sem a configuração do balanceador de carga ou de múltiplos NICs ou vários IP reservados.

### <a name="restoring-from-powershell"></a>Restaurando do PowerShell:
O PowerShell tem a capacidade de restaurar apenas os discos da VM do backup, e não criar a máquina virtual. Isso é útil ao restaurar máquinas virtuais que exigem as configurações de rede especiais mencionadas acima.

Para recriar completamente a máquina virtual após restaurar os discos, execute estas etapas:

1. Restaure os discos do cofre de backup usando o [PowerShell do Backup do Azure](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Crie a configuração da VM necessária para o balanceador de carga/múltiplos NICs, múltiplos IPs reservados usando os cmdlets do PowerShell e use-a para criar a VM com a configuração desejada.
   
   * Criar a VM no serviço de nuvem com o [balanceador de carga interno ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Criar a VM para se conectar ao [balanceador de carga voltado para a Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Criar uma VM [com várias NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Criar VMs com [vários IPs reservados](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Próximas etapas
* [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)


