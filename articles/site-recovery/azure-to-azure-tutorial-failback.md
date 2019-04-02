---
title: Fazer failback de VMs do Azure replicadas para uma região do Azure secundária para recuperação de desastre com o serviço do Azure Site Recovery.
description: Saiba como fazer failback de VMs do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315310"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Failback de VMs do Azure entre regiões do Azure

O serviço do [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação de desastre, gerenciando e orquestrando replicação, failover e failback de computadores locais e VMs (máquinas virtuais) do Azure.

Este tutorial descreve como fazer failback de uma única VM do Azure. Depois de fazer failover, você fará failback para a região primária quando ela estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> 
> * Fazer failback da VM do Azure na região secundária.
> * Proteger novamente a VM primária do Azure de volta à região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda você a fazer failover de algumas VMs para uma região de destino de volta para a região de origem com as personalizações mínimas. Para obter mais instruções detalhadas, examine os artigos em “Como” para VMs do Azure.

## <a name="before-you-start"></a>Antes de começar

> * Certifique-se de que a VM está no estado **Failover confirmado**.
> * Verifique se a região primária está disponível e, em seguida, você poderá criar e acessar os novos recursos nela.
> * Certifique-se de que a nova proteção está habilitada.

## <a name="fail-back-to-the-primary-region"></a>Failback para a região primária

Depois que as VMs forem protegidas novamente, você poderá fazer failback para a região primária, conforme necessário.

1. No cofre, clique em **Itens Replicados** e selecione a VM que foi protegida novamente.

    ![Failback para primária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Clique em **Failover de Teste** para executar um failover de teste de volta para a região primária.
4. Selecione o ponto de recuperação e a rede virtual para o failover de teste e clique em **OK**. É possível examinar a VM de teste criada na região primária.
5. Depois que o failover de teste for concluído com êxito, clique em **Limpar failover de teste** para limpar os recursos criados na região de origem para o failover de teste.
6. Em **Itens replicados**, selecione a VM e clique em **Failover**.
7. Em **Failover**, selecione um ponto de recuperação para o qual fazer failover.
    - **Mais recente (padrão)**: processa todos os dados no serviço Site Recovery e fornece o menor RPO (Objetivo de Ponto de Recuperação).
    - **Mais recente processado**: Reverte a VM para o último ponto de recuperação que foi processado pelo Site Recovery.
    - **Personalizado**: Faz failover para um determinado ponto de recuperação. Essa opção é útil para fazer um failover de teste.

8. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das VMs de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Observe que o Site Recovery não limpa a origem após o failover.
9. Acompanhe o progresso do failover na página **Trabalhos**.
10. Após o failover, valide a VM fazendo logon nela. É possível alterar o ponto de recuperação conforme necessário.
11. Depois de verificar o failover, clique em **Fazer commit do failover**. Confirmar exclui todos os pontos de recuperação disponíveis. A opção Alterar ponto de recuperação não está mais disponível.
12. A VM deve aparecer como o fazer failover realizado e failback realizado.

    ![VM nas regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> As VMs de recuperação de desastre permanecerão no estado desalocado de desligamento. Isso ocorre intencionalmente porque o Site Recovery salva as informações da VM, que podem ser úteis para o failover da região primária para a secundária posteriormente. Você não é cobrado pelas VMs desalocadas e, portanto, elas devem ser mantidas como elas estão.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
