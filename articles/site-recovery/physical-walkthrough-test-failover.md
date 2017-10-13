---
title: "Execute um failover de teste para replicação do servidor físico para Azure com Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para executar um failover de teste para [servidores físicos replicando para o Azure utilizando o serviço Azure Site Recovery."
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
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>Etapa 11: Executar um failover de teste de servidores físicos para Azure

Este artigo descreve como executar um failover de teste de servidores físicos locais para o Azure, utilizando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

Antes de executar um failover de teste, é recomendável verificar as propriedades do servidor e efetuar as alterações necessárias. é possível acessar as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.

## <a name="managed-disk-considerations"></a>Considerações sobre discos gerenciados

[Discos gerenciados](../virtual-machines/windows/managed-disks-overview.md) simplificam o gerenciamento de disco para VMs do Azure, gerenciando as contas de armazenamento associadas aos discos da VM. 

- Ao habilitar a proteção para um servidor, os dados da VM replicam para uma conta de armazenamento. Os discos gerenciados são criados e anexados à VM somente quando ocorrer o failover.
- Discos gerenciados podem ser criados somente para VMs do Azure implantadas utilizando o modelo do Resource Manager.  
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

É recomendável verificar as propriedades do servidor de origem antes de executar um failover.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e, em seguida, clique na máquina.
2. No painel **Item Replicado** é possível visualizar um resumo das informações da máquina, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
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

1. Para fazer failover em uma máquina única, em **Configurações** > **Itens Replicados**, clique na máquina > ícone **+Test Failover**.

    ![Failover de Teste](./media/physical-walkthrough-test-failover/test-failover.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).  

3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.

4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na máquina para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.

5. Após a conclusão do failover, você também deverá visualizar a réplica da VM do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.

6. Se você tiver se preparado para conexões após o failover, você deverá poder se conectar à VM do Azure.

### <a name="delete-test-failover-vms"></a>Excluir VMs de failover de teste

1. Após concluir, clique no **Failover de teste de limpeza** no plano de recuperação ou máquina.
2. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.
3. A ação de limpeza excluirá as VMs do Azure que foram criadas durante o failover de teste.

## <a name="summary"></a>Resumo

Se você concluiu o teste de failover com êxito, seus servidores físicos estão replicando e você verificou se eles podem fazer failover no Azure. Agora, é possível executar failovers de acordo com seus requisitos organizacionais. 

Lembre-se de que atualmente não é possível executar o failback do Azure para um servidor físico. É necessário executar o failback para uma VM do VMware. Isso significa que você precisa de uma infraestrutura de VMware local para executar o failback. [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre executar o failback na VM do Azure para VMware.


## <a name="next-steps"></a>Próximas etapas

- [Executar failovers](site-recovery-failover.md) conforme necessário.
