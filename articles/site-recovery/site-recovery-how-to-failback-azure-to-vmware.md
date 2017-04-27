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
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: b054245de2c5bf6019c2cb29409289f2ac8766ec
ms.lasthandoff: 03/31/2017


---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Failback do Azure para um site local
Este artigo descreve como realizar o failback de máquinas virtuais das Máquinas Virtuais do Azure para o site local. Siga as instruções neste artigo para fazer o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando o tutorial [Replicar máquinas virtuais VMware e servidores físicos para o Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Visão geral do failback
Veja como o failback funciona. Após a realização do failover para o Azure, execute o failback para seu site local por meio de alguns estágios:

1. [Proteja novamente](site-recovery-how-to-reprotect.md) as máquinas virtuais no Azure para que iniciem a replicação para máquinas virtuais VMware seu site local. Como parte desse processo, você também precisa:
    1. Configurar um destino mestre local: destino mestre do Windows para máquinas virtuais Windows e [destino mestre Linux](site-recovery-how-to-install-linux-master-target.md) para máquinas virtuais Linux.
    2. Configure um [servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Inicie o [proteger novamente](site-recovery-how-to-reprotect.md).
5. Depois que as máquinas virtuais do Azure estiverem sendo replicadas no site local, você executará um failover do Azure para o site local.

Após realizar o failback dos dados, você protege novamente as máquinas virtuais locais para as quais realizou o failback, para que comecem a ser replicadas no Azure.

Para obter uma visão geral, assista a este vídeo sobre como fazer failover do Azure para um site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Failback para o local original ou alternativo

Se você fez failover de uma máquina virtual VMware, você pode executar failback para a mesma máquina de virtual do local de origem se ele ainda existe. Nessa situação, apenas as alterações passarão por failback. Esse cenário é conhecido como recuperação no local original. Se a máquina virtual no local não existir, o cenário é uma recuperação em local alternativo.

#### <a name="original-location-recovery"></a>Recuperação no local original

Se você não volta para a máquina virtual original, as seguintes condições são necessárias:
* Se a máquina virtual for gerenciada por um servidor vCenter, o host do ESX de Destino Mestre deve ter acesso ao repositório de dados das máquinas virtuais.
* Se a máquina virtual estiver em um host ESX, mas não é gerenciada pelo vCenter, o disco rígido da máquina virtual deve ser em um repositório de dados que o host do destino mestre pode acessar.
* Se sua máquina virtual estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do destino mestre antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.
* Você pode executar failback para uma rede de área de armazenamento virtual (vSAN) ou um disco com base no mapeamento (RDM) se os discos já existem e estão conectados à máquina virtual local de dispositivo bruto.

#### <a name="alternate-location-recovery"></a>Recuperação em local alternativo
Se a máquina virtual no local não existe antes de proteger novamente a máquina virtual, o cenário é chamado uma recuperação em local alternativo. O fluxo de trabalho Proteja cria novamente a máquina virtual no local. Isso também fará um download de dados completo.

* Ao realizar o failback para um local alternativo, a máquina virtual será recuperada no mesmo host ESX no qual o servidor de destino mestre local está implantado. O armazenamento de dados que é usado para criar o disco será o mesmo armazenamento de dados que foi selecionado quando proteger novamente a máquina virtual.
* Você pode falhar novamente somente para um repositório de dados de sistema (VMFS) de arquivo de máquina virtual. Se você tiver uma vSAN ou RDM, o proteger novamente e o Failback não funcionarão.
* O proteger novamente envolve uma grande transferência inicial de dados seguida pelas alterações. Esse processo existe porque a máquina virtual não existe no local. Os dados completos precisam ser replicada de volta. O proteger novamente levará mais tempo do que em uma recuperação no local original.
* Não é possível executar failback para discos com base em vSAN ou RDM. Somente novos discos de máquina virtual (VMDKs) podem ser criados em um datastore VMFS.

Um computador físico, após o failover no Azure, só pode passar pelo failback como uma máquina virtual do VMware (também conhecida como P2A2V). Esse fluxo se enquadra na recuperação em local alternativo.

* Um servidor com Windows Server 2008 R2 SP1 se protegido e com failover no Azure não pode passar por failback.
* Descubra, pelo menos, um servidor de destino mestre juntamente com os hosts ESX/ESXi necessários para os quais você precisa fazer failback.

## <a name="have-you-completed-reprotection"></a>Você concluiu o proteger novamente?
Antes de prosseguir, conclua as etapas de Nova Proteção para que as máquinas virtuais estejam em estado replicado e você possa iniciar um failover em um local. Para saber mais, veja [Como proteger mais do Azure para locais](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Pré-requisitos

* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração ou o failback não obterá êxito. Sendo assim, faça o backup agendado regular de seu servidor. Em caso de um desastre, você precisará restaurar o servidor com o mesmo endereço IP para que o failback funcione.
* O servidor de destino mestre não deve ter instantâneos antes de disparar o failback.

## <a name="steps-to-fail-back"></a>Etapas para failback

> [!IMPORTANT]
Antes de iniciar o failback, certifique-se de ter concluído o proteger novamente das máquinas virtuais. As máquinas virtuais devem estar em um estado protegido e sua integridade deve ser **OK**. Para proteger novamente as máquinas virtuais, leia [como proteger novamente](site-recovery-how-to-reprotect.md).

1. Na página Itens replicados, selecione a máquina virtual e clique com o botão direito do mouse para selecionar **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação (o mais recente, ou um que seja consistente com o aplicativo mais recente) que você deseja usar para o failover. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
3. Durante o failover, o Site Recovery desliga as máquinas virtuais no Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as máquinas virtuais do Azure foram desligadas.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para o ponto de recuperação pode não consigo novamente as máquinas virtuais?

Durante o failback, você tem duas opções para failback o plano de recuperação/máquina virtual.

Se você selecionar o ponto no tempo processado mais recentemente, todas as máquinas virtuais serão submetidas a failover para o ponto no tempo mais recente disponível no momento. Caso haja um grupo de replicação no plano de recuperação, cada máquina virtual do grupo de replicação será submetida a failover para seu ponto no tempo independente mais recente.

Você não pode efetuar novamente uma máquina virtual até que tenha pelo menos um ponto de recuperação. Não é possível failback um plano de recuperação até que todas as suas máquinas virtuais tenham pelo menos uma recuperação de ponto.

> [!NOTE]
> Um ponto de recuperação mais recente é um ponto de recuperação consistente com falha.

Se você selecionar o ponto de recuperação consistente de aplicativo, um único failback de máquina virtual será recuperado para o ponto de recuperação consistente de aplicativo disponível mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação será recuperado para o ponto de recuperação disponível comum.
Observe que os pontos de recuperação consistentes de aplicativo podem ser anteriores no tempo, e pode haver perda de dados.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão do failback, você precisa confirmar a máquina virtual para assegurar que máquinas virtuais recuperadas no Azure são excluídas.

### <a name="commit"></a>Confirmar
A confirmação é necessária para remover a máquina virtual com failover do Azure.
Clique com o botão direito no item protegido e então clique em **Confirmar**. Um trabalho removerá as máquinas virtuais anteriormente recuperadas no Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure

Após a confirmação, a máquina virtual for novamente no site local, mas ele não estará protegido. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. Em **Cofre** > **Configuração** > **Itens replicados**, selecione as máquinas virtuais na quais foi executado o failback e clique em Proteger **novamente**.
2. Forneça o valor do servidor de processo que precisa ser usado para enviar dados de volta ao Azure.
3. Clique em **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma máquina virtual local é inicializada, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, proteja falhará e retornará uma mensagem de erro informando que o agente não está instalado. Aguarde alguns minutos e tente proteger novamente.

Após o trabalho Proteja novamente, a máquina virtual está replicando para o Azure e você pode fazer um failover.

## <a name="common-issues"></a>Problemas comuns
Verifique se o vCenter está em um estado conectado antes de fazer um failback. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falhará.

