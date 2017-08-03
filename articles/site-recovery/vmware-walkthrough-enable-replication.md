---
title: "Habilitar a replicação de VMs VMware que replicam para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para habilitar a replicação de VMs VMware para o Azure, usando o serviço Azure Site Recovery"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 470b9ddd8df4a4e74ec7174f79020c252323e502
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017


---
# <a name="step-11-enable-replication-for-vmware-virtual-machines-to-azure"></a>Etapa 11: Habilitar replicação de máquinas virtuais VMware para o Azure


Este artigo descreve como habilitar a replicação de VMs VMware locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

- As VMs VMware devem ter o [componente Serviço de mobilidade instalado](vmware-walkthrough-install-mobility.md). - Se uma VM for preparada para a instalação por push, o servidor de processo instalará automaticamente o serviço Mobilidade quando você habilitar a replicação.
- Sua conta de usuário do Azure precisa de [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) específicas para habilitar a replicação de uma VM para o Azure
- Quando você adiciona ou modifica as VMs, pode levar 15 minutos ou mais para que as alterações entrem em vigor e para que apareçam no portal.
- Você pode verificar a hora da última descoberta de VMs em **Servidores de Configuração** > **Último Contato às**.
- Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.



## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb). [Saiba mais](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replicar VMs

Antes de começar, assista a uma rápida visão geral em vídeo

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse será o servidor de configuração. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/vmware-walkthrough-enable-replication/enable-replication2.png)

6. Em **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs do failover.


7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. Se não quiser usar uma conta que já configurou, você poderá criar uma nova.

8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se não quiser usar uma rede existente, você poderá criar uma.

    ![Habilitar a replicação](./media/vmware-walkthrough-enable-replication/enable-rep3.png)
9. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/vmware-walkthrough-enable-replication/enable-replication5.png)
10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
11. Por padrão, todos os discos são replicados. Clique em **Todos os Discos** e desmarque os discos que você não deseja replicar. Em seguida, clique em **OK**. Você pode definir propriedades de VM adicionais posteriormente.

    ![Habilitar a replicação](./media/vmware-walkthrough-enable-replication/enable-replication6.png)
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Se você modificar uma política, as alterações serão aplicadas à máquina de replicação e às novas máquinas.
12. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

    * Os computadores nos grupos de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
    * É recomendável que você colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e precisam de consistência.

    ![Habilitar a replicação](./media/vmware-walkthrough-enable-replication/enable-replication7.png)
13. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 12: Executar um failover de teste](vmware-walkthrough-test-failover.md)

