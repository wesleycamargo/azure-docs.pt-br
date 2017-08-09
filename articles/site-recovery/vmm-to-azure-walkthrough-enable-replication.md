---
title: "Habilitar a replicação no Azure para VMs do Hyper-V em nuvens do VMM com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como habilitar a replicação no Azure para VMs do Hyper-V em nuvens do VMM, com o serviço do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: b8a8bacd73ae9f6c7b7c982a18d55b8bd5d42c76
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>Etapa 11: Habilitar a replicação no Azure para VMs do Hyper-V em nuvens de VMM

Depois de configurar a política de replicação, use este artigo para permitir a replicação para VMs (máquinas virtuais) do Hyper-V locais gerenciadas em nuvens do System Center Virtual Machine Manager (VMM) para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

Verifique se sua conta do Azure tem as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) corretas para criar VMs do Azure. [Saiba mais](../active-directory/role-based-access-built-in-roles.md) sobre o controle de acesso baseado em função do Azure.

## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb). [Saiba mais](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replicar VMs

Habilite a replicação para VMs conforme demonstrado a seguir:  

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.

    ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. Na folha **Origem**, selecione o servidor do VMM e a nuvem na qual os hosts do Hyper-V estão localizados. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. Em **Destino**, selecione a assinatura, o modelo de implantação pós-failover e a conta de armazenamento que você está usando para os dados replicados.

    ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. Selecione a conta de armazenamento que você deseja usar. Se quiser usar uma conta de armazenamento diferente da que você tem, poderá [criar uma](#set-up-an-azure-storage-account). Se você estiver usando uma conta de armazenamento premium para os dados replicados, precisará selecionar uma conta de armazenamento standard adicional para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais. Para criar uma conta de armazenamento usando o modelo do Resource Manager, clique em **Criar nova**. Se você quiser criar uma conta de armazenamento usando o modelo clássico, faça isso no [portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Em seguida, clique em **OK**.
5. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se quiser usar uma rede diferente da que você tem, poderá [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo do Gerenciador de Recursos, clique em **Criar novo**. Se você quiser criar uma rede usando o modelo clássico, terá de fazer isso [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.
6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco do sistema operacional.

    - Verifique se o nome da VM do Azure (nome de destino) está em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Por padrão, todos os discos da VM são selecionados para replicação, mas você pode limpar os discos para excluí-los.

        - Talvez você queira excluir discos para reduzir a largura de banda de replicação. Por exemplo, talvez você não queira replicar discos com os dados temporários ou dados que são atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou Microsoft SQL Server tempdb). Para excluir o disco da replicação, você deverá desmarcá-lo.
        - Apenas discos básicos podem ser excluídos. Você não pode excluir discos do sistema operacional.
        - É recomendável que você não exclua discos dinâmicos. O Site Recovery não pode identificar se um disco rígido virtual dentro de uma VM convidada é básico ou dinâmico. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco protegido dinâmico aparecerá como um disco com falha quando ocorrer o failover da VM e os dados nesse disco não poderão ser acessados.
        - Depois que a replicação estiver habilitada, você não poderá adicionar ou remover discos para replicação. Se desejar adicionar ou excluir um disco, você precisará desabilitar a proteção da VM e habilitá-la novamente.
        - Discos que você criar manualmente no Azure não sofrerão failback. Por exemplo, se você realizar failover de três discos e criar dois discos diretamente na VM do Azure, apenas três discos que sofreram failover sofrerão failback do Azure para o Hyper-V. Você não pode incluir discos criados manualmente em failback ou em replicação inversa do Hyper-V para o Azure.
        - Se você excluir um disco necessário para um aplicativo operar, após o failover no Azure você precisará criá-lo manualmente no Azure para que possa executar o aplicativo replicado. Como alternativa, integre a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.

    Clique em **OK** para salvar as alterações. Você pode definir propriedades adicionais posteriormente.

    ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. Em **Configurações de replicação** > **Definir configurações de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Em seguida, clique em **OK**. É possível modificar a política de replicação em **Políticas de replicação** > nome da política > **Editar Configurações**. As alterações aplicadas são usadas para computadores que já estejam replicando e para novas máquinas.

   ![Habilitar a replicação](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

É possível acompanhar o progresso do trabalho **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 12: Executar um failover de teste](vmm-to-azure-walkthrough-test-failover.md)

