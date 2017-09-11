---
title: "Executar um failover de teste para replicação do VMware no Azure | Microsoft Docs"
description: "Resume as etapas necessárias para executar um failover de teste para VMs VMware fazendo a replicação para o Azure utilizando o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f1a6df56a2bb0094d972d2e659057cc124156b88
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="step-12-run-a-test-failover-to-azure-for-vmware-vms"></a>Etapa 12: executar um failover de teste para o Azure para VMs VMware

Este artigo descreve como executar um failover de teste de máquinas virtuais VMware locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

Antes de executar um failover de teste, é recomendável verificar as propriedades da VM e efetuar as alterações necessárias. é possível acessar as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.

## <a name="managed-disk-considerations"></a>Considerações sobre discos gerenciados

[Discos gerenciados](../virtual-machines/windows/managed-disks-overview.md) simplificam o gerenciamento de disco para VMs do Azure, gerenciando as contas de armazenamento associadas aos discos da VM. 

- Ao habilitar a proteção para uma VM, os dados dela são replicados para uma conta de armazenamento. Os discos gerenciados são criados e anexados à VM somente quando ocorrer o failover.
- Discos gerenciados podem ser criados somente para VMs implantadas utilizando o modelo do Resource Manager.  
- Com essa configuração habilitada, apenas os conjuntos de disponibilidade nos Grupos de Recursos que tenham a configuração **Utilizar discos gerenciados** habilitados poderão ser selecionados. VMs com discos gerenciados devem estar em conjuntos de disponibilidade com a configuração **Utilizar discos gerenciados** configurada para **Sim**. Se a configuração não estiver habilitada para as VMs, apenas os conjuntos de disponibilidade em Grupos de Recursos sem discos gerenciados habilitados poderão ser selecionados.
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) sobre discos gerenciados e conjuntos de disponibilidade.
- Se a conta de armazenamento que você utiliza para replicação foi criptografada com a Criptografia do Serviço de Armazenamento, os discos gerenciados não poderão ser criados durante o failover. Nesse caso, não habilite o uso de discos gerenciados ou desabilite a proteção para a VM e reabilite-a para utilizar uma conta de armazenamento que não tenha criptografia habilitada. [Saiba mais](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>Considerações sobre rede

É possível definir o endereço IP de destino para uma VM do Azure criada após o failover.

- Se você não fornecer um endereço, o computador com failover usará o DHCP.
- Se definir um endereço que não esteja disponível no failover, o failover não funcionará.
- O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino:

     - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
     - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
     - Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino der suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.     
   - Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
   - Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na máquina virtual do Azure.
 - [Saiba mais](vmware-walkthrough-network.md) sobre o endereçamento IP.



## <a name="view-and-modify-vm-settings"></a>Exibir e modificar as configurações da VM

É recomendável verificar as propriedades do computador de origem antes de executar um failover.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e clique na VM.
2. No painel **Item Replicado** é possível visualizar um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computador e Rede**, é possível:
    - Modificar o nome da VM do Azure. O nome deve atender aos [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Especificar um failover de postagem [grupo de recursos].
    - Especifique um tamanho de destino para a VM do Azure
    - Selecione um [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md).
    - Especifique se deseja usar [discos gerenciados](#managed-disk-considerations). Selecione **Sim**, se deseja anexar discos gerenciados à sua máquina na migração para o Azure.
    - Visualize ou modifique as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após o failover e o endereço IP que será atribuído.
4. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Depois de configurar tudo, execute um failover de teste para conferir se tudo está funcionando conforme o esperado.

- Se você deseja se conectar às VMs do Azure utilizando o RDP após o failover, [prepare-se para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para um teste completo, você precisa copiar o Active Directory e o DNS para seu ambiente de teste. [Saiba mais](site-recovery-active-directory.md#test-failover-considerations).
 - Para obter informações completas sobre failover de teste, leia [este artigo](site-recovery-test-failover-to-azure.md).
- Obtenha uma rápida visão geral em vídeo antes de começar:


>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


Agora, execute um failover:

1. Para fazer failover em um único computador, em **Configurações** > **Itens Replicados**, clique na VM > ícone **+Failover de Teste**.

    ![Failover de Teste](./media/vmware-walkthrough-test-failover/test-failover.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).  

3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.

4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.

5. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.

6. Se você tiver se preparado para conexões após o failover, você deverá poder se conectar à VM do Azure.

7. Após concluir, clique no **Failover de teste de limpeza** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. Isso excluirá as VMs que foram criadas durante o failover de teste.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Se estiver migrando máquinas em vez de replicar e fazer failback, [leia mais](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [Leia sobre failback](site-recovery-failback-azure-to-vmware.md), para executar failback e replicar VMs do Azure de volta para o site primário local.

