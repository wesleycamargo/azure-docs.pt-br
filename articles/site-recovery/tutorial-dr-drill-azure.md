---
title: "Executar uma análise detalhada da recuperação de desastre em computadores locais para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba mais sobre como executar a análise detalhada da recuperação de desastre localmente para o Azure, com o Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Realizar uma análise detalhada da recuperação de desastre para o Azure

Este tutorial mostra como executar uma análise detalhada da recuperação de desastre para máquinas locais no Azure usando um failover de teste. Uma análise detalhada valida sua estratégia de replicação, sem perda de dados. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma rede isolada para o failover de teste
> * Preparar-se para conectar-se à VM do Azure após o failover
> * Executar um failover de teste para um único computador

Este é o quarto tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](tutorial-prepare-on-premises-vmware.md)
3. [Configurar a recuperação de desastre](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover de teste, confira as propriedades da VM e verifique se a VM está em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado.
   
      >[!NOTE]
      No momento, o failback para máquinas Hyper-V locais de VMs do Azure com discos gerenciados não é compatível. Você só deverá usar a opção de discos gerenciados para o failover se planejar migrar VMs do local para o Azure, sem fazer seu failback.
   
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-test-failover-for-a-single-vm"></a>Executar um failover de teste para uma VM

Quando você executar um failover de teste, acontecerá o seguinte:

1. Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para o failover foram cumpridas.
2. O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
3. Uma VM do Azure é criada usando os dados processados na etapa anterior.

Execute o failover de teste da seguinte maneira:

1. Em **Configurações** > **Itens Replicados**, clique na VM > **+Failover de Teste**.
2. Selecione o ponto de recuperação **Último processado** para este tutorial. Isto fará failover da VM para o ponto no tempo mais recente disponível. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados, portanto, um RTO (Objetivo de Tempo de Recuperação) baixo é fornecido.
3. Em **Failover de Teste**, selecione a rede de destino do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** >
   **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução.
6. Agora você certamente poderá se conectar à VM replicada no Azure.
7. Para excluir as VMs do Azure criadas durante o failover de teste, clique em **Limpar failover de teste** na VM. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo em computadores VMware Linux, em VMs VMware que não têm o serviço DHCP habilitado e em VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar failover e failback de VMs locais do VMware](tutorial-vmware-to-azure-failover-failback.md).
