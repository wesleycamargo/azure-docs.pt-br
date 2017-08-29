---
title: "Executar um failover de teste para replicação de Hyper-V (sem o System Center VMM) para o Azure | Microsoft Docs"
description: "Resume as etapas necessárias para executar um failover de teste para VMs Hyper-V fazendo a replicação para o Azure utilizando o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c9a4c3ca-84a0-4668-b700-9b0046202299
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0974b9eda2cb7e3ba54a4a0fad0a768db644caf9
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---

# <a name="step-11-run-a-test-failover-for-hyper-v-replication-to-azure"></a>Etapa 11: Executar um failover de teste para replicação do Hyper-V para o Azure

Este artigo descreve como executar um failover de teste de máquinas virtuais Hyper-V locais (não gerenciadas pelo System Center VMM) para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar

Antes de executar um failover de teste, é recomendável verificar as propriedades da VM e efetuar as alterações necessárias. é possível acessar as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.

## <a name="managed-disk-considerations"></a>Considerações sobre discos gerenciados

[Discos gerenciados](../virtual-machines/windows/managed-disks-overview.md) simplificam o gerenciamento de disco para VMs do Azure, gerenciando as contas de armazenamento associadas aos discos da VM. 

- Os discos gerenciados são criados e anexados à VM somente quando um failover no Azure ocorre. Quando você habilita a proteção, os dados das VMs locais são replicados em contas de armazenamento.
- Managed disks podem ser criados apenas para VMs implantadas usando o modelo de implantação do Resource Manager.
- Não há suporte para failback do Azure para o ambiente do Hyper-V local para computadores com managed disks. Você só deve definir **Usar discos gerenciados** como **Sim** se estiver fazendo uma migração única (failover no Azure sem failback)
- Com essa configuração habilitada, apenas os conjuntos de disponibilidade nos Grupos de Recursos que tenham a configuração **Utilizar discos gerenciados** habilitados poderão ser selecionados. VMs com discos gerenciados devem estar em conjuntos de disponibilidade com a configuração **Utilizar discos gerenciados** configurada para **Sim**. Se a configuração não estiver habilitada para as VMs, apenas os conjuntos de disponibilidade em Grupos de Recursos sem discos gerenciados habilitados poderão ser selecionados. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).
- - Se a conta de armazenamento que você utiliza para replicação foi criptografada com a Criptografia do Serviço de Armazenamento, os discos gerenciados não poderão ser criados durante o failover. Nesse caso, não habilite o uso de discos gerenciados ou desabilite a proteção para a VM e reabilite-a para utilizar uma conta de armazenamento que não tenha criptografia habilitada. [Saiba mais](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).

 
## <a name="network-considerations"></a>Considerações sobre rede
    
- É possível definir o endereço IP de destino a ser usado para a VM do Azure após o failover. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover falhará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:
    - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
    - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
    - Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.     
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
- Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na máquina virtual do Azure.


## <a name="view-and-manage-vm-settings"></a>Exibir e gerenciar as configurações da VM

É recomendável verificar as propriedades do computador de origem antes de executar um failover.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e clique na VM.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-test-failover/test-failover1.png)
2. No painel **Item Replicado** é possível visualizar um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-test-failover/test-failover2.png)
3. Em **Computador e Rede**, é possível:
    - Modificar o nome da VM do Azure. O nome deve atender aos [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Especificar um failover de postagem [grupo de recursos].
    - Especifique um tamanho de destino para a VM do Azure
    - Selecione um [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md).
    - Especifique se deseja usar [discos gerenciados](#managed-disk-considerations). Selecione **Sim**, se deseja anexar discos gerenciados à sua máquina na migração para o Azure.
    - Visualize ou modifique as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após o failover e o endereço IP que será atribuído.

    ![Habilitar a replicação](./media/hyper-v-site-walkthrough-test-failover/test-failover4.png)
4. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.


## <a name="run-a-test-failover"></a>Execute um teste de failover

Agora, execute um failover de teste para verificar se tudo está funcionando como esperado.

- Se você deseja se conectar às VMs do Azure utilizando o RDP após o failover, [prepare-se para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para um teste completo, você precisa copiar o Active Directory e o DNS para seu ambiente de teste. [Saiba mais](site-recovery-active-directory.md#test-failover-considerations).
 - Para obter informações completas sobre failover de teste, leia [este artigo](site-recovery-test-failover-to-azure.md).
 
 Agora, execute um failover:

1. Para executar failover de um único computador, em **Itens Replicados**, clique na VM > ícone **+Failover de Teste**.
2. Para executar failover de um plano de recuperação, em **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).
3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. É possível acompanhar o progresso clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
6. Se você tiver se preparado para conexões após o failover, você deverá poder se conectar à VM do Azure.
7. Após concluir, clique no **Failover de teste de limpeza** no plano de recuperação. Em **Observações** , registre e salve todas as observações associadas ao failover de teste. Isso excluirá as máquinas virtuais que foram criadas durante o failover de teste.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- [Leia sobre failback](site-recovery-failback-from-azure-to-hyper-v.md), para executar failback e replicar VMs do Azure de volta para o site do Hyper-V local.


