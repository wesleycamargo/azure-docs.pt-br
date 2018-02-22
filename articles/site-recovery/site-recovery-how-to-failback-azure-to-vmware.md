---
title: Como fazer failback do Azure para o VMware | Microsoft Docs
description: "Após o failover de máquinas virtuais no Azure, você pode iniciar um failback para colocar máquinas virtuais para o local. Conheça as etapas de como realizar failback."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: 7a76ff45fe6209698532a7abc2d561bf7a7b3284
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Failback do Azure para um site local

Este artigo descreve como realizar o failback de máquinas virtuais das Máquinas Virtuais do Azure para o ambiente VMware. Siga as instruções neste artigo para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando o tutorial [Failover no Site Recovery](site-recovery-failover.md).

## <a name="prerequisites"></a>pré-requisitos
- Certifique-se de que você tenha lido os detalhes sobre os [tipos diferentes de failback](concepts-types-of-failback.md) e as perdas correspondentes.

> [!WARNING]
> Não é possível realizar o failback após [concluir a migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), mover a máquina virtual para outro grupo de recursos ou excluir a máquina virtual do Azure. Se você desabilitar a proteção da máquina virtual, não é possível realizar o failback.

> [!WARNING]
> Um servidor físico com Windows Server 2008 R2 SP1, se protegido e com failover no Azure, não pode passar por failback.

> [!NOTE]
> Se você fizer failover em máquinas virtuais VMware, não poderá fazer failback para um host Hyper-v.


- Antes de prosseguir, conclua as etapas de Nova Proteção para que as máquinas virtuais estejam em estado replicado e você possa iniciar um failover em um local. Para saber mais, veja [Como proteger mais do Azure para locais](site-recovery-how-to-reprotect.md).

- Verifique se o vCenter está em um estado conectado antes de fazer um failback. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.

- Durante o failover para o Azure, o site local pode não estar acessível e, portanto, o servidor de configuração pode não estar disponível ou estar desligado. Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e em um estado conectado OK. 

- Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração ou o failback não obterá êxito. Sendo assim, faça o backup agendado regular de seu servidor. Em caso de um desastre, você precisa restaurar o servidor com o endereço IP original, para que o failback funcione.

- O servidor de destino mestre não deve ter instantâneos antes de acionar o failback ou a nova proteção.

## <a name="overview-of-failback"></a>Visão geral do failback
Após a realização do failover para o Azure, será possível executar o failback para seu site local executando as seguintes etapas:

1. [Proteja novamente](site-recovery-how-to-reprotect.md) as máquinas virtuais no Azure para que iniciem a replicação para máquinas virtuais VMware seu site local. Como parte desse processo, você também precisa:
    1. Configurar um destino mestre local: destino mestre do Windows para máquinas virtuais Windows e [destino mestre Linux](site-recovery-how-to-install-linux-master-target.md) para máquinas virtuais Linux.
    2. Configure um [servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Inicie o [proteger novamente](site-recovery-how-to-reprotect.md). Isso irá desligar a máquina virtual no local e sincronizar os dados da máquina virtual do Azure com os discos no local.

1. Depois que as máquinas virtuais do Azure estiverem sendo replicadas no site local, você executará um failover do Azure para o site local.

1. Após realizar o failback dos dados, você protege novamente as máquinas virtuais locais para as quais realizou o failback, para que comecem a ser replicadas no Azure.

Para obter uma visão geral, assista a este vídeo sobre como fazer failover para um site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Etapas para failback

> [!IMPORTANT]
> Antes de iniciar o failback, certifique-se de ter concluído o proteger novamente das máquinas virtuais. As máquinas virtuais devem estar em um estado protegido e sua integridade deve ser **OK**. Para proteger novamente as máquinas virtuais, leia [como proteger novamente](site-recovery-how-to-reprotect.md).

1. Na página Itens replicados, selecione a máquina virtual e clique com o botão direito do mouse para selecionar **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação (o mais recente, ou um que seja consistente com o aplicativo mais recente) que você deseja usar para o failover. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
3. Durante o failover, o Site Recovery desliga as máquinas virtuais no Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as máquinas virtuais do Azure foram desligadas.
4. **Confirmar** é necessário para remover o failover da máquina virtual do Azure. Clique com o botão direito do mouse no item protegido e clique em **Confirmar**. Um trabalho removerá as máquinas virtuais anteriormente recuperadas no Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para o ponto de recuperação pode não consigo novamente as máquinas virtuais?

Durante o failback, você tem duas opções para failback o plano de recuperação/máquina virtual.

- Se você selecionar o ponto no tempo processado mais recentemente, todas as máquinas virtuais serão submetidas a failover para o ponto no tempo mais recente disponível no momento. Caso haja um grupo de replicação no plano de recuperação, cada máquina virtual do grupo de replicação será submetida a failover para seu ponto no tempo independente mais recente.

    Você não pode efetuar novamente uma máquina virtual até que tenha pelo menos um ponto de recuperação. Não é possível failback um plano de recuperação até que todas as suas máquinas virtuais tenham pelo menos uma recuperação de ponto.

> [!NOTE]
> Um ponto de recuperação mais recente é um ponto de recuperação consistente com falha.

- Se você selecionar o ponto de recuperação consistente de aplicativo, um único failback de máquina virtual será recuperado para o ponto de recuperação consistente de aplicativo disponível mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação é recuperado para o ponto de recuperação disponível comum.
Os pontos de recuperação consistentes de aplicativo podem ser anteriores no tempo, e pode haver perda de dados.

## <a name="what-happens-to-vmware-tools-post-failback"></a>O que acontece com o failback de postagem de ferramentas VMware?

No caso de uma máquina virtual do Windows, o Azure Site Recovery desabilita as ferramentas VMware durante o failover. Durante o failback da máquina virtual do Windows, as ferramentas do VMware são habilitadas novamente. 


## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure
Depois que o failback terminar e você tiver iniciado a confirmação, as máquinas virtuais recuperadas no Azure serão excluídas. Agora, a máquina virtual está novamente no site local, mas ela não estará protegida. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. Em **Cofre** > **Configuração** > **Itens replicados**, selecione as máquinas virtuais na quais foi executado o failback e clique em Proteger **novamente**.
2. Forneça o valor do servidor de processo que precisa ser usado para enviar dados de volta ao Azure.
3. Clique em **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma máquina virtual local é inicializada, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, proteja falhará e retornará uma mensagem de erro informando que o agente não está instalado. Aguarde alguns minutos e tente proteger novamente.

## <a name="next-steps"></a>Próximas etapas

Depois que o trabalho de nova proteção for concluído, a máquina virtual será replicada novamente para o Azure e você poderá fazer um [failover](site-recovery-failover.md) a fim de mover as máquinas virtuais do Azure.


