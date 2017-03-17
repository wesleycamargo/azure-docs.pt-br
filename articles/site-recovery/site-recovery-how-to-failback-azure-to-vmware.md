---
title: Como fazer failback do Azure para o Hyper-v | Microsoft Docs
description: "Após o failover de máquinas virtuais no Azure, você pode iniciar um failback para trazer as máquinas virtuais de volta para o local. Aprenda as etapas para fazer um failback."
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
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>Failback do Azure para o local
Este artigo descreve como realizar o failback de máquinas virtuais do Azure para o site local. Siga as instruções deste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Visão geral do failback
Veja como o failback funciona - após a realização do failover para o Azure, execute o failback para seu site local por meio de alguns estágios:

1. [Proteja novamente](site-recovery-how-to-reprotect.md) as VMs do Azure para que elas comecem a ser replicadas de volta nas VMs do VMware em execução no site local. Para isso, você também precisa 
    1. Configurar um destino de Mestre local - Windows MT para VMs do Windows e [Linux MT](site-recovery-how-to-install-linux-master-target.md) para VMs do Linux
    2. Configurar um [servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md)
    3. E, em seguida, inicie o modo [Proteger Novamente](site-recovery-how-to-reprotect.md)
5. Depois que as VMs do Azure estiverem sendo replicadas no site local, você executará um failover do Azure para o local.
  
Após realizar o failback dos dados, você protege novamente as VMs locais para as quais realizou o failback, para que comecem a ser replicadas no Azure.

Para obter uma visão geral em vídeo, você também pode usar o vídeo aqui.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Failback para o local original ou para um local alternativo
    
Se você realizou o failover de uma VM do VMware, poderá realizar o failback para a mesma VM de origem, caso ela ainda exista localmente. Nessa situação, apenas as alterações delta passarão por failback. Esse cenário é conhecido como recuperação no local original. Se a VM local não existir, será uma recuperação em local alternativo.

#### <a name="original-location-recovery"></a>Recuperação no local original

Se você realizar o failback para a VM original, precisará do seguinte:
* Se a VM for gerenciada por um servidor vCenter, o host do ESX de Destino Mestre deve ter acesso ao repositório de dados das máquinas virtuais.
* Se a VM estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da VM deverá estar em um repositório de dados acessível ao host do MT.
* Se sua VM estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do MT antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.
* Você poderá executar o failback para discos com base em vSAN ou RDM se os discos já existirem e estiverem conectados à VM local.

#### <a name="alternate-location-recovery"></a>Recuperação em local alternativo
Se a VM local não existir antes de proteger novamente a VM, chamaremos de recuperação em local alternativo. Aqui, o fluxo de trabalho Proteja Novamente cria novamente a VM local. Isso também fará um download de dados completo.

* Ao realizar o failback para um local alternativo, a VM será recuperada no mesmo host ESX no qual o servidor de destino mestre local está implantado. O repositório de dados usado para criar o disco será o mesmo repositório de dados selecionado ao proteger novamente a VM.
* Você pode realizar o failback apenas para um repositório de dados VMFS. Se você tiver uma vSAN ou RDM, o Proteja Novamente e o Failback não funcionarão.
* O Proteja Novamente envolve uma grande transferência inicial de dados seguida pelas alterações delta. Isso ocorre porque a VM não existe localmente e, por isso, os dados completos precisam ser replicados de volta. O Proteja Novamente levará mais tempo do que na recuperação no local original.
* Não é possível executar failback para discos com base em vSAN ou RDM. Somente novos VMDKs podem ser criados em um datastore VMFS.

Um computador físico, após o failover no Azure, só pode passar pelo failback como uma máquina virtual do VMware (também conhecida como P2A2V). Esse fluxo se enquadra na recuperação em local alternativo.

* Um computador com Windows Server 2008 R2 SP1 se protegido e com failover no Azure não pode passar por failback.
* Certifique-se de descobrir, pelo menos, um servidor de Destino Mestre juntamente com os hosts ESX/ESXi necessários para os quais você precisa fazer failback.

## <a name="have-you-completed-reprotection"></a>Você concluiu a Nova Proteção?
Antes de prosseguir, conclua as etapas de Nova Proteção para que as máquinas virtuais estejam em estado replicado e você possa iniciar um failover no local.
[Como proteger novamente do Azure para o local](site-recovery-how-to-reprotect.md).

## <a name="pre-requisites"></a>Pré-requisitos
 
* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados de servidor de configuração. Caso contrário, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurá-lo com o mesmo endereço IP para que o failback funcione.
* O servidor de destino mestre não deve ter instantâneos antes de disparar o failback.

## <a name="steps-to-failback"></a>Etapas para failback

Antes de iniciar o failback, **certifique-se de que você concluiu o Proteja Novamente das máquinas virtuais**. As máquinas virtuais devem estar no estado protegido com sua integridade indicada como OK. Para proteger novamente as máquinas virtuais, leia mais sobre [como proteger novamente](site-recovery-how-to-reprotect.md).

1. Na página Itens replicados, selecione a máquina virtual e clique com o botão direito do mouse para selecionar **Failover Não Planejado**.
2. Em **Confirmar Failover** , verifique a direção do failover (para o Azure) e selecione o ponto de recuperação que você deseja usar para o failover (o mais recente, ou um que seja consistente com o aplicativo mais recente). O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
3. Durante o failover, a Recuperação de Site desligará as VMs do Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as VMs do Azure foram desligadas.

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>Para qual ponto de recuperação posso executar o failback das VMs?

Durante o failback, você tem duas opções para executar o failback para VM/Plano de Recuperação.

Se você selecionar o ponto no tempo processado mais recentemente, todas as máquinas virtuais serão submetidas a failover para o ponto no tempo mais recente disponível no momento. Caso haja um grupo de replicação no plano de recuperação, cada VM do grupo de replicação será submetida a failover para seu ponto no tempo independente mais recente.

Não é possível executar o failback para uma VM até que ela tenha pelo menos um ponto de recuperação. Não é possível executar o failback para um plano de recuperação até que todas as VMs tenham pelo menos um ponto de recuperação.

> [!NOTE]
> Um ponto de recuperação mais recente é um ponto de recuperação consistente com falha.

Se você selecionar o ponto de recuperação consistente de aplicativo, um único failback de VM será recuperado para o ponto de recuperação consistente de aplicativo disponível mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação será recuperado para o ponto de recuperação disponível comum.
Observe que os pontos de recuperação consistentes de aplicativo podem ser anteriores no tempo, e pode haver perda de dados.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão do failback, você precisará confirmar a máquina virtual para garantir que as VMs recuperadas no Azure sejam excluídas.

### <a name="commit"></a>Confirmar
A confirmação é necessária para remover a máquina virtual com failover do Azure.
Clique com o botão direito do mouse no item protegido e clique em Confirmar. Será disparado um trabalho que removerá as máquinas virtuais anteriormente recuperadas no Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure

Após a conclusão da confirmação, sua VM voltará ao site local, mas não estará protegida. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. No Cofre > Configuração > Itens, replicados, selecione as VMs na quais foi executado o failback e clique em **Proteger Novamente**.
2. Forneça o valor do Servidor em processo que precisa ser usado para enviar dados de volta ao Azure.
3. Clique em OK para iniciar o trabalho de nova proteção.

> [!NOTE]
> Depois que uma VM for inicializada no local, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, você verá que a nova proteção falhará, e uma mensagem de erro informará que o agente não está instalado. Aguarde alguns minutos e tente Proteger novamente.

Quando o trabalho de nova proteção for concluído, a VM será replicada de volta ao Azure e você poderá executar um failover.

## <a name="common-issues"></a>Problemas comuns
* Verifique se o vCenter está em um estado conectado antes de fazer um failback. Caso contrário, a desconexão dos discos e sua reanexação de volta à VM falharão.


