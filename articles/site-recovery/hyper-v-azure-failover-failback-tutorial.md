---
title: Fazer failover e failback de VMs do Hyper-V replicadas no Azure com o Site Recovery | Microsoft Docs
description: Saiba como fazer failover de VMs do Hyper-V no Azure e failback no site local com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294285"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Fazer failover e failback em VMs do Hyper-V replicadas no Azure

Este tutorial descreve como fazer failover de uma VM do Hyper-V no Azure. Após fazer failover, faça failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM Hyper-V para saber se elas estão em conformidade com os requisitos do Azure
> * Fazer um failover para o Azure
> * Failback do Azure para o local
> * Faça replicação inversa de VMs locais para iniciar a replicação para o Azure novamente

Este tutorial é o quinto tutorial de uma série. Presume-se que você já tenha concluído as tarefas nos tutoriais anteriores.    

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurar a recuperação de desastres para as [VMs do Hyper-V](tutorial-hyper-v-to-azure.md) ou para [VMs do Hyper-V gerenciadas em nuvens do System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparar-se para failover e failback

Certifique-se de que não haja instantâneos na VM e que a VM local esteja desativada durante o failback. Isso ajuda a garantir a consistência dos dados durante a replicação. Não ative a VM local durante o failback. 

O failover e o failback têm três estágios:

1. **Failover para Azure**: fazer failover de VMs do Hyper-V do site local para Azure.
2. **Failback para local**: fazer failover de VMs do Azure para o site local, quando disponível. Ele começa a replicar as VMs de volta para as VMs do Hyper-V locais. Após a sincronização inicial de dados, execute failover de VMs do Azure para o site local.  
3. **Replicação inversa de VMs locais**: após o failback dos dados, replique inversamente as VMs locais para começar a replicá-las para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes do failover, verifique as propriedades da VM e verifique se a VM atende aos [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > <nome da VM>.

2. No painel **Item Replicado**, examine as informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
     - Em **Computação e Rede**, é possível modificar as configurações de VM e de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após o failover e o endereço IP que será atribuído a ela. Os discos gerenciados não são compatíveis com failback do Azure para Hyper-V.
      - Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="failover-to-azure"></a>Failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione o ponto de recuperação **Mais recente**. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar as VMs de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Depois de verificar o failover, clique em **Confirmar**. Exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele um failover em andamento**: se você cancelar em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Fazer failback de VM do Azure para local e replicação inversa de VM local

A operação de failback é basicamente um failover do Azure para o site local e, na replicação inversa, ele inicia novamente a replicação de VMs do site local para o Azure.

1. Em **Configurações** > **Itens Replicados**, clique na VM > **Failover Planejado**.
2. Em **Confirmar Failover Planejado**, verifique a direção do failover (do Azure) e selecione os locais de origem e de destino.
3. Selecione **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**. Essa opção minimiza o tempo de inatividade da VM porque sincroniza sem desligar a VM.
4. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
5. Depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as VMs no Azure, clique em **Trabalhos** > nome do trabalho de failover planejado > **Concluir Failover**. Ele desliga a VM do Azure, transfere as alterações mais recentes para o local e inicia a VM local.
6. Faça logon na VM local para verificar se ela está disponível conforme o esperado.
7. A VM local agora está em um estado de **Confirmação Pendente**. Clique em **Confirmar**. Ele exclui as VMs do Azure e seus discos e prepara a VM local para a replicação inversa.
Para iniciar a replicação da VM local para o Azure, habilite a **Replicação Inversa**. Ele dispara a replicação de alterações delta ocorridas desde que a VM do Azure foi desativada.  
