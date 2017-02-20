---
title: Como fazer failback do Azure para o local | Microsoft Docs
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
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 1a6dfb60f5db8b842af27b4c88bc3e5927331622
ms.openlocfilehash: 20446aadbb7bc3d13c5a38de5c4040f5898d4f6b


---
# <a name="failback-from-azure-to-on-premises"></a>Failback do Azure para o local
Este artigo descreve como realizar o failback de máquinas virtuais do Azure para o site local. Siga as instruções deste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Visão geral
Veja a seguir como o failback funciona:

* Após a realização do failover para o Azure, execute o failback para seu site local por meio de alguns estágios:
  * **Estágio 1**: você protege novamente as VMs do Azure para que elas comecem a serem replicadas de volta nas VMs do VMware em execução no site local. Para isso, você também precisa 
        1. Configurar um servidor de destino mestre local
        2. Configurar um servidor de processo
        3. E, em seguida, iniciar o modo Proteja Novamente
  * **Estágio 2**: assim que as VMs do Azure estiverem sendo replicadas no site local, você executa um failover para realizar o failback no Azure.
  * **Estágio 3**: após realizar o failback de seus dados, você protege novamente as VMs locais para as quais você realizou o failback, para que comecem a serem replicadas no Azure.

### <a name="failback-to-the-original-or-alternate-location"></a>Failback para o local original ou para um local alternativo

    
Se você realizou o failover de uma VM do VMware, poderá realizar o failback para a mesma VM de origem, caso ela ainda exista localmente. Nessa situação, apenas as alterações delta passarão por failback. Esse cenário é conhecido como recuperação no local original.

* Se você realizar o failback para a VM original, precisará do seguinte:
  * Se a VM for gerenciada por um servidor vCenter, o host do ESX de Destino Mestre deve ter acesso ao repositório de dados das máquinas virtuais.
  * Se a VM estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da VM deverá estar em um repositório de dados acessível ao host do MT.
  * Se sua VM estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do MT antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.

  
Se a VM local não existir antes de proteger novamente a VM, chamaremos de recuperação em local alternativo. Aqui, o fluxo de trabalho Proteja Novamente cria novamente a VM local.

* Ao realizar o failback para um local alternativo, a VM será recuperada no mesmo host ESX no qual o servidor de destino mestre local está implantado. O repositório de dados usado para criar o disco será o mesmo repositório de dados selecionado ao proteger novamente a VM.
* Você pode realizar o failback apenas para um repositório de dados VMFS. Se você tiver uma vSAN ou RDM, o Proteja Novamente e o Failback não funcionarão.
* O Proteja Novamente envolve uma grande transferência inicial de dados seguida pelas alterações delta. Isso ocorre porque a VM não existe localmente e, por isso, os dados completos precisam ser replicados de volta. O Proteja Novamente levará mais tempo do que na recuperação no local original.

Um computador físico, após o failover no Azure, só pode passar pelo failback como uma máquina virtual do VMware (também conhecida como P2A2V). Esse fluxo se enquadra na recuperação em local alternativo.

* Um computador com Windows Server 2008 R2 SP1 se protegido e com failover no Azure não pode passar por failback.
* Certifique-se de descobrir, pelo menos, um servidor de Destino Mestre juntamente com os hosts ESX/ESXi necessários para os quais você precisa fazer failback.


## <a name="pre-requisites"></a>Pré-requisitos

* Se as VMs para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, você precisará das permissões necessárias para a descoberta das VMs nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se houver instantâneos em uma VM local, o Proteja Novamente falhará. Você pode excluir os instantâneos antes de prosseguir para o Proteja Novamente.
* Antes de realizar o failback, será necessário criar dois componentes adicionais:
  * **Criar um servidor de processo**. O servidor de processo é usado para receber os dados da VM protegida no Azure e enviar os dados ao local. Isso exige que ele esteja em uma rede de baixa latência entre o servidor de processo e a VM protegida. Portanto, o servidor de processo poderá ser local (se você estiver usando uma conexão de rota expressa) ou no Azure (se você estiver usando uma VPN).
  * **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento criado no local tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback. 
        * Uma VM do Linux precisa de um servidor de destino mestre do Linux. 
        * Uma VM do Windows precisa de um destino mestre do Windows.
* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados de servidor de configuração. Caso contrário, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurá-lo com o mesmo endereço IP para que o failback funcione.
* Verifique se você definiu a configuração disk.enableUUID=true nos Parâmetros de Configuração da VM de destino Mestre no VMware. Se essa linha não existir, adicione-a. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente.
* **O servidor de destino mestre não pode ser armazenado com vMotioned**. Isso pode causar a falha do failback. A VM não será mostrada, pois os discos não serão disponibilizados para ela.
* Você precisa de uma nova unidade adicionada ao servidor de destino Mestre. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade.


## <a name="steps-to-failback"></a>Etapas para failback

Antes de iniciar o failback, **certifique-se de que você concluiu o Proteja Novamente das máquinas virtuais**. As máquinas virtuais devem estar no estado protegido com sua integridade indicada como OK. Para proteger novamente as máquinas virtuais, leia mais sobre [como proteger novamente](site-recovery-how-to-reprotect.md).


1. Na página Itens replicados, selecione a máquina virtual e clique com o botão direito do mouse para selecionar **Failover Não Planejado**.
2. Em **Confirmar Failover** , verifique a direção do failover (para o Azure) e selecione o ponto de recuperação que você deseja usar para o failover (o mais recente, ou um que seja consistente com o aplicativo mais recente). O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
3. Durante o failover, a Recuperação de Site desligará as VMs do Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as VMs do Azure foram desligadas.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão do failback, você precisará confirmar a máquina virtual para garantir que as VMs recuperadas no Azure sejam excluídas.

1. Clique com o botão direito do mouse no item protegido e clique em Confirmar. Será disparado um trabalho que removerá as máquinas virtuais anteriormente recuperadas no Azure.

Após a conclusão da confirmação, sua VM voltará ao site local, mas não estará protegida. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. No Cofre > Configuração > Itens, replicados, selecione as VMs na quais foi executado o failback e clique em **Proteger Novamente**.
2. Forneça o valor do Servidor em processo que precisa ser usado para enviar dados de volta ao Azure.
3. Clique em OK para iniciar o trabalho de nova proteção.

Quando o trabalho de nova proteção for concluído, a VM será replicada de volta ao Azure e você poderá executar um failover.

## <a name="common-issues"></a>Problemas comuns





<!--HONumber=Feb17_HO2-->


