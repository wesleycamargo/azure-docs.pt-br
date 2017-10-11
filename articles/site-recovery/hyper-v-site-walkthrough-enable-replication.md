---
title: "Habilitar a replicação de VMs Hyper-V que replicam para o Azure (sem o System Center VMM) com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para habilitar a replicação de VMs Hyper-V para o Azure, usando o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>Etapa 10: habilitar replicação para VMs do Hyper-V em replicação para o Azure


Este artigo descreve como habilitar a replicação de máquinas virtuais Hyper-V locais (não gerenciadas pelo System Center VMM) para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique se a sua conta de usuário do Azure tem as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessárias para habilitar a replicação de uma nova máquina virtual no Azure.

## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb). [Saiba mais](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>Replicar VMs

Habilite a replicação para VMs conforme demonstrado a seguir:          

1. Clique em **Replicar aplicativo** > **Origem**. Depois de configurar a replicação pela primeira vez, você pode clicar em **+Replicar** para habilitar a replicação para outros computadores.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. Em **Origem**, selecione o site do Hyper-V. Em seguida, clique em **OK**.
3. Em **Destino**, selecione a assinatura de cofre e o modelo de failover que você deseja usar no Azure (gerenciamento de recursos ou clássico) após o failover.
4. Selecione a conta de armazenamento que você deseja usar. Se não tiver uma conta que deseje usar, você poderá [criar uma](#set-up-an-azure-storage-account). Em seguida, clique em **OK**.
5. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.

    - Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores habilitados para replicação.
    - Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
    - Se não tiver uma rede que deseje usar, você poderá [criar uma](#set-up-an-azure-network). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.

   ![Habilitar a replicação](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco do sistema operacional.
8. Verifique se o nome da VM do Azure (nome de destino) está em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Por padrão, todos os discos da VM são selecionados para replicação. Limpar discos para excluí-los.
10. Clique em **OK** para salvar as alterações. Você pode definir propriedades adicionais posteriormente.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. Em **Configurações de replicação** > **Definir configurações de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Em seguida, clique em **OK**. É possível modificar a política de replicação em **Políticas de replicação** > nome da política > **Editar Configurações**. As alterações aplicadas serão usadas para computadores que já estejam replicando e para novas máquinas.


   ![Habilitar a replicação](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

É possível acompanhar o progresso do trabalho **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.


## <a name="next-steps"></a>Próximas etapas


Acesse a [Etapa 11: Executar um failover de teste](hyper-v-site-walkthrough-test-failover.md)
