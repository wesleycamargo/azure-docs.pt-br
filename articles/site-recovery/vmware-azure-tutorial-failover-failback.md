---
title: Executar failover e failback de VMs VMware e de servidores físicos durante a recuperação de desastre para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como executar failover de VMs VMware e de servidores físicos para o Azure e o failback para o site local, durante a recuperação de desastre, com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357215"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Fazer failover e failback de VMs VMware

Este artigo descreve como fazer failover de uma VM VMware local para o serviço [Azure Site Recovery](site-recovery-overview.md) do Azure. 

Este é o quinto tutorial em uma série que mostra como configurar a recuperação de desastres para o Azure para computadores locais.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM VMware para saber se elas estão em conformidade com os requisitos do Azure
> * Executar um failover para o Azure


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Se você quiser saber mais sobre failover em detalhes, [leia este artigo](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de começar
Conclua os tutoriais anteriores:

1. Verifique se você [configurou o Azure](tutorial-prepare-azure.md) para recuperação de desastre local de VMs VMware, VMs do Hyper-V e computadores físicos para o Azure.
2. Prepare seu ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) local para recuperação de desastre. Se você estiver configurando a recuperação de desastre para servidores físicos, examine a [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configure a recuperação de desastre para [VMs VMware](vmware-azure-tutorial.md), [VMs Hyper-V](hyper-v-azure-tutorial.md) ou [computadores físicos](physical-azure-disaster-recovery.md).
4. Execute uma [simulação de recuperação de desastres](tutorial-dr-drill-azure.md) para verificar se tudo está funcionando conforme o esperado.


## <a name="failover-and-failback"></a>Failover e failback

O failover e o failback têm quatro fases:

1. **Fazer failover para o Azure**: Quando seu site primário local fica inativo, faça failover de computadores para o Azure. Após o failover, as VMs do Azure são criadas com base nos dados replicados.
2. **Proteger novamente as VMs do Azure**: No Azure, proteja as VMs do Azure novamente, para que elas comecem a replicação para as VMs VMware locais. A VM local é desativada durante o processo de nova proteção para ajudar a garantir a consistência dos dados.
3. **Executar failover para o local**: Quando o seu site local está ativo e em execução, execute um failover para realizar failback do Azure.
4. **Proteger novamente as VMs locais**: Após realizar o failback dos dados, você protege novamente as VMs locais para as quais realizou o failback, para que comecem a ser replicadas no Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover, verifique as propriedades da VM e se as VMs estão em conformidade com os [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte maneira:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado

4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.

5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar um failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
   - **Mais recente**: Essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Mais recente processado**: Essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: Essa opção executa failover da VM para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: Especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes de iniciar o failover** para tentar fazer um desligamento das VMs de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode observar tempos de failover de teste mais longos para:
- VMs VMware executando uma versão de serviço de Mobilidade anterior à 9.8
- Servidores físicos
- VMs VMware Linux
- VMs Hyper-V protegidas como servidores físicos
- VMs VMware que não têm o serviço DHCP habilitado
- VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Não cancele um failover em andamento**: Antes de iniciar o failover, a replicação de VM será interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="connect-to-failed-over-vm"></a>Conectar-se à VM com failover

1. Se você quiser se conectar às VMs do Azure usando o RDP/SSH após o failover, [verifique estes requisitos](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Após o failover, vá para a VM e valide [conectando](../virtual-machines/windows/connect-logon.md)-se a ela.
3. Use **Alterar ponto de recuperação** se você quiser usar um ponto de recuperação diferente após o failover. Após você confirmar o failover na próxima etapa, essa opção não estará mais disponível.
4. Após a validação, clique em **Confirmar** para finalizar o ponto de recuperação da VM após o failover.
5. Após a confirmação, todos os outros pontos de recuperação disponíveis são excluídos. Isso conclui o failover.

>[!TIP]
> Se você encontrar problemas de conectividade após o failover, siga este [guia de solução de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

Após o failover, proteja novamente as VMs do Azure para o local. Em seguida, depois que as VMs estiverem protegidas novamente e replicando para o site local, faça failback do Azure quando estiver pronto.

> [!div class="nextstepaction"]
> [Proteger novamente VMs do Azure](vmware-azure-reprotect.md)
> [Failback do Azure](vmware-azure-failback.md) 
