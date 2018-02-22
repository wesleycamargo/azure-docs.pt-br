---
title: Executar failover e failback de VMs do Hyper-V replicadas no Azure com o Site Recovery | Microsoft Docs
description: Saiba como executar failover de VMs do Hyper-V no Azure e failback no site local com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: 7d2d99c2429a461307cbb9a276eb3b62d13718d2
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Executar failover e failback em VMs do Hyper-V replicadas no Azure

Este tutorial descreve como executar failover de uma VM do Hyper-V no Azure. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM Hyper-V para saber se elas estão em conformidade com os requisitos do Azure
> * Executar um failover para o Azure
> * Proteger novamente as VMs do Azure para o site local
> * Failback do Azure para o local
> * Proteger novamente as VMs locais, para iniciar novamente a replicação para o Azure

Este é o quinto tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurar a recuperação de desastres para as [VMs do Hyper-V](tutorial-hyper-v-to-azure.md) ou para [VMs do Hyper-V gerenciadas em nuvens do System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparar-se para failover e failback

Verifique se não há instantâneos na VM e se a VM local está desativada durante a nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação. Não ative a VM após a conclusão da nova proteção. 

O failover e o failback têm quatro fases:

1. **Failover para o Azure**: faz failover de computadores do site local para o Azure.
2. **Proteger novamente as VMs do Azure**: proteja as VMs do Azure novamente, para que elas comecem a replicação para as VMs Hyper-V locais.
3. **Failover para o local**: execute um failover do Azure para seu site local, quando ele estiver disponível.
4. **Proteger novamente as VMs locais**: após o failback do dados, proteja as VMs locais novamente para que elas iniciem a replicação para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Confira as propriedades da VM e verifique se a VM está em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > <nome da VM>.

2. No painel **Item Replicado**, examine as informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
     - Em **Computação e Rede**, você pode modificar as configurações de VM e as configurações de rede, incluindo a rede/sub-rede da VM do Azure. Os discos gerenciados não são compatíveis com failback do Azure para Hyper-V.
   será localizado depois do failover e o endereço IP que será atribuído a ele.
    - Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="fail-over-to-azure"></a>Fazer failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione o ponto de recuperação **Mais recente**. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar as VMs de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Depois de verificar o failover, clique em **Confirmar**. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="reprotect-azure-vms"></a>Proteger as VMs do Azure

1. Em **AzureVMVault** > **Itens replicados**, clique com o botão direito do mouse na VM em que o failover foi executado e, em seguida, selecione **Proteger Novamente**.
2. Verifique se a direção da proteção está definida como **Azure para local**.
3. A VM local é desativada durante o processo de nova proteção para ajudar a garantir a consistência dos dados. Não a ative após a conclusão da nova proteção.
4. Após o processo de nova proteção, a VM iniciar a replicação do Azure para o site local.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Fazer failover do Azure e proteger novamente a VM local

Faça failover do Azure para o site local e inicie a replicação das VMs do site local para o Azure.

1. Em **Configurações** > **Itens Replicados**, clique na VM > **Failover Planejado**.
2. Em **Confirmar Failover Planejado**, verifique a direção do failover (do Azure) e selecione os locais de origem e de destino.
3. Selecione **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**. Essa opção minimiza o tempo de inatividade da VM porque sincroniza sem desligar a VM.
4. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
5. Depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as VMs no Azure, clique em **Trabalhos** > nome do trabalho de failover planejado > **Concluir Failover**. Isso desliga a VM do Azure, transfere as alterações mais recentes para o local e inicia a VM local.
6. Faça logon na VM local para verificar se ela está disponível conforme o esperado.
7. A VM local agora está em um estado de **Confirmação Pendente**. Clique em **Confirmar**. Isso exclui as VMs do Azure e seus discos e prepara a VM local para a replicação inversa.
Para iniciar a replicação da VM local para o Azure, habilite a **Replicação Inversa**. Isso dispara a replicação de alterações delta ocorridas desde que a VM do Azure foi desativada.  
