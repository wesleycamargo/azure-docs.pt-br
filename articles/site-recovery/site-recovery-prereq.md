---
title: "Pré-requisitos para replicação no Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo resume os pré-requisitos para replicação de VMs e computadores físicos no Azure usando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 5ff598af73b6be727753ecac5b99f28bae19a417
ms.lasthandoff: 04/17/2017

---

#  <a name="prerequisites-for-replication-to-azure-by-using-azure-site-recovery"></a>Pré-requisitos para replicação no Azure usando o Azure Site Recovery


O serviço Azure Site Recovery contribui para a continuidade e a estratégia de recuperação de desastres (BCDR) do seu negócio ao orquestrar a replicação de servidores físicos locais e máquinas virtuais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você pode fazer failover em um local secundário a fim de manter aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você pode realizar o failback. Para mais informações sobre o Site Recovery, consulte [O que é Site Recovery?](site-recovery-overview.md).

Este artigo resume os pré-requisitos necessários para começar a replicação do Site Recovery no Azure.

Poste comentários no final do artigo ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Serviço do Site Recovery** | Para obter mais informações sobre os preços do Site Recovery, consulte [Preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Armazenamento do Azure** | Você precisa de uma conta de armazenamento do Azure para armazenar os dados replicados e ela deve estar na mesma região que o cofre dos Serviços de Recuperação. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover.<br/><br/> Dependendo do modelo de recurso que você deseja usar para o failover de VMs do Azure, você poderá configurar uma conta no [modelo do Azure Resource Manager](../storage/storage-create-storage-account.md) ou no [modelo clássico](../storage/storage-create-storage-account-classic-portal.md).<br/><br/>Você pode usar [armazenamento com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) ou armazenamento com redundância local. É recomendável usar armazenamento com redundância geográfica para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região primária não puder ser recuperada.<br/><br/> Você pode usar o armazenamento Standard ou Armazenamento Premium. O [Armazenamento premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) normalmente é usada para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho intensivas de E/S de host. Com o armazenamento premium para dados replicados, você também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.<br/><br/>
**Limitações de armazenamento** | Não é possível mover contas de armazenamento usadas pelo Site Recovery entre grupos de recursos, ou em ou entre assinaturas.<br/><br/> No momento, não há suporte para replicação em contas de armazenamento premium na Índia Central e no Sul da Índia.
**Rede do Azure** | Você precisa de uma rede do Azure à qual as VMs do Azure se conectarão após o failover e ela deve estar na mesma região que o cofre dos Serviços de Recuperação.<br/><br/> No portal do Azure, você pode criar redes no [modelo do Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modelo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Se você replicar do System Center Virtual Machine Manager para o Azure, configurará o mapeamento de rede entre redes de VM do Virtual Machine Manager e do Azure para garantir que as VMs do Azure se conectem às redes apropriadas após o failover.
**Limitações de rede** | Não é possível mover contas de rede usadas pelo Site Recovery entre grupos de recursos, ou em ou entre assinaturas.
**Mapeamento de rede** | Se você replicar VMs do Hyper-V em nuvens do Virtual Machine Manager, será preciso configurar o mapeamento de rede para que as VMs do Azure sejam conectadas às redes apropriadas quando elas forem criadas após o failover.

>[!NOTE]
>As seções a seguir descrevem os pré-requisitos de vários componentes no ambiente do cliente. Para saber mais sobre o suporte para configurações específicas, leia a [matriz de suporte](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windows-or-linux-servers-to-azure"></a>Recuperação de desastre de máquinas virtuais VMware ou servidores físicos Windows ou Linux no Azure
A seguir estão os componentes obrigatórios para a recuperação de desastres de máquinas virtuais VMware ou servidores físicos Windows ou Linux, além dos mencionados nos [requisitos do Azure](#Azure requirements).


### <a name="configuration-server-or-additional-process-server-you-will-need-to-set-up-an-on-premises-machine-as-the-configuration-server-to-coordinate-communications-between-the-on-premises-site-and-azure-and-to-manage-data-replication-brbr"></a>**Servidor de configuração ou servidor de processo adicional**: você precisará configurar um computador local como o servidor de configuração para coordenar a comunicação entre o site local e o Azure, bem como para gerenciar a replicação de dados. <br></br>

1. **Host do VMware vCenter ou vSphere**

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Um ou mais hipervisores do VMware vSphere.<br/><br/>Os hipervisores devem estar executando o vSphere versão 6.0, 5.5 ou 5.1 com as últimas atualizações.<br/><br/>Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede que o servidor de processo. Essa é a rede na qual o servidor de configuração está localizado, a menos que você tenha configurado um servidor de processo dedicado. |
| **vCenter** | Recomendamos que você implante um servidor VMware vCenter para gerenciar seus hosts vSphere. Ele deve executar o vCenter versão 6.0 ou 5.5, com as últimas atualizações.<br/><br/>**Limitação**: o Site Recovery não dá suporte entre o vMotion e vCenter. Também não há suporte para o Armazenamento DRS e Storage vMotion na máquina virtual de destino mestre após uma operação de nova proteção.||

1. **Pré-requisitos de computadores replicados**


| **Componente** | **Requisitos** |
| --- | --- |
| **Local** (VMs VMware) | As VMs replicadas devem ter as ferramentas VMware instaladas e em execução.<br/><br/> Para que seja possível criar VMs do Azure, elas devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).<br/><br/>A capacidade do disco individual nos computadores protegidos não deve ser maior que 1.023 GB. <br/><br/>Um mínimo de 2 GB de espaço disponível na unidade de instalação é necessário para a instalação do componente.<br/><br/>A porta 20004 deverá ser aberta no firewall local da VM se você desejar habilitar a consistência de múltiplas VMs.<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois de habilitar a replicação em um computador, você poderá modificar o nome do Azure.<br/><br/> |
| **Computadores Windows** (físicos ou VMware) | O computador deve estar executando um sistema operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com, ao menos, SP1.<br/><br/> O sistema operacional deve ser instalado na unidade C. O disco do sistema operacional deve ser um disco básico do Windows, e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/>|
| **Computadores com Linux** (físicos ou VMware) | Você precisa de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7, 6.8, 7.1 ou 7.2; CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1 ou 7.2; Oracle Enterprise Linux 6.4 ou 6.5 executando o kernel compatível do Red Hat ou o Unbreakable Enterprise Kernel Versão 3 (UEK3), SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4.<br/><br/>Seus arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.<br/><br/>Se você quiser se conectar a uma máquina virtual do Azure que executa o Linux após o failover usando um cliente Secure Shell (ssh), verifique se o serviço do Secure Shell na máquina protegida está definido para ser iniciado automaticamente na inicialização do sistema e se as regras de firewall permitem uma conexão ssh para ele.<br/><br/>O nome do host, os pontos de montagem, os nomes de dispositivos, os caminhos do sistema Linux e os nomes de arquivos (por exemplo: /etc/; /usr) devem estar somente em inglês.<br/><br/>Os diretórios a seguir (se configurados como partições/sistemas de arquivos separados) devem estar no mesmo disco (disco do sistema operacional) no servidor de origem: / (raiz), /boot, /usr, /usr/local, /var e /etc<br/><br/>No momento não há suporte para recursos do XFS v5, como a soma de verificação de metadados, pela ASR em sistemas de arquivos XFS. Verifique se seus sistemas de arquivos XFS não estão usando recursos da v5. Você pode usar o utilitário xfs_info para verificar o super-bloco XFS da partição. Se ftype está definido como 1, os recursos XFSv5 estão sendo usados.<br/><br/>Em servidores Red Hat Enterprise Linux 7 e CentOS 7, o utilitário lsof deve estar instalado e disponível.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager"></a>Recuperação de Desastre de máquinas virtuais do Hyper-V no Azure (sem Virtual Machine Manager)

A seguir estão os componentes obrigatórios para a recuperação de desastres de máquinas virtuais Hyper-V nas nuvens do Virtual Machine Manager, além dos mencionados nos [requisitos do Azure](#Azure requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Host Hyper-V** |Um ou mais servidores locais executando o Windows Server 2012 R2 com a função de Hyper-V e as atualizações mais recentes habilitadas ou o Microsoft Hyper-V Server 2012 R2.<br/><br/>Os servidores Hyper-V devem conter uma ou mais máquinas virtuais.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter correções mencionadas no [KB2961977](https://support.microsoft.com/kb/2961977) instalado.
|**Provedor e agente**| Durante a implantação do Azure Site Recovery, você instalará o Provedor do Azure Site Recovery. A instalação do Provedor também instalará o Agente dos Serviços de Recuperação do Azure em cada servidor Hyper-V que executar máquinas virtuais que você deseja proteger. <br/><br/>Todos os servidores Hyper-V em um cofre de Recuperação de Site devem ter as mesmas versões do Provedor e do agente.<br/><br/>O Provedor precisará se conectar ao Azure Site Recovery pela Internet. O tráfego pode ser enviado diretamente ou por meio de um proxy. Não há suporte para o proxy com base em HTTPS. O servidor proxy deve permitir o acesso a: <br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure"></a>Recuperação de desastres de máquinas virtuais Hyper-V em nuvens do Virtual Machine Manager para o Azure

A seguir estão os componentes obrigatórios para a recuperação de desastres de máquinas virtuais Hyper-V nas nuvens do Virtual Machine Manager, além dos mencionados nos [requisitos do Azure](#Azure requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Virtual Machine Manager** |Um ou mais servidores do Virtual Machine Manager em execução no **System Center 2012 R2 ou posterior**. Cada servidor do Virtual Machine Manager deve ter uma ou mais nuvens configuradas. <br/><br/>Uma nuvem deve conter:<br>- Um ou mais grupos de hosts do Virtual Machine Manager.<br/>- Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.<br/><br/>Para mais informações sobre como configurar nuvens do Virtual Machine Manager, consulte [Como criar uma nuvem no VMM 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx). |
| **Hyper-V** |Os servidores de host do Hyper-V devem estar executando pelo menos o Windows Server 2012 R2 com a função Hyper-V ou o Microsoft Hyper-V Server 2012 R2 e ter as atualizações mais recentes instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/> Um servidor ou cluster de host Hyper-V que inclui VMs que você deseja replicar deve ser gerenciado em uma nuvem do Virtual Machine Manager.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Os servidores Hyper-V devem ter as correções mencionadas no [artigo 2961977](https://support.microsoft.com/kb/2961977) instaladas.<br/><br/>Os servidores de host do Hyper-V precisam de acesso à Internet para a replicação de dados no Azure. |
| **Provedor e agente** |Durante a implantação do Azure Site Recovery, instale o provedor Azure Site Recovery no servidor do Virtual Machine Manager e instale o Agente de Serviços de Recuperação nos hosts Hyper-V. O provedor e o agente precisam se conectar ao Azure pela Internet, diretamente ou por meio de um proxy. Não há suporte para um proxy com base em HTTPS. O servidor proxy no servidor do Virtual Machine Manager e os hosts Hyper-V devem permitir o acesso a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se você tiver regras de firewall baseadas no endereço IP do servidor do Virtual Machine Manager, certifique-se de que as regras permitam a comunicação com o Azure.<br/><br/> Permita os [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/>Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de computadores replicados
| **Componente** | **Detalhes** |
| --- | --- |
| **VMs protegidas** | O Site Recovery é compatível com todos os sistemas operacionais que são aceitos pelo [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Para que seja possível criar VMs do Azure, elas devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. <br/><br/>Você pode modificar o nome depois de habilitar a replicação para a VM. <br/><br/> A capacidade do disco individual nos computadores protegidos não deve ser maior que 1.023 GB. Uma VM pode ter até 16 discos (portanto, até 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-a-customer-owned-site"></a>Recuperação de desastre de máquinas virtuais do Hyper-V em nuvens do Virtual Machine Manager em um site de propriedade do cliente

A seguir estão os componentes obrigatórios para a recuperação de desastres de máquinas virtuais Hyper-V nas nuvens do Virtual Machine Manager em um site de propriedade do cliente, além dos mencionados nos [requisitos do Azure](#Azure requirements).

| **Componentes** | **Detalhes** |
| --- | --- |
| **Virtual Machine Manager** |  Recomendamos que você implante um servidor do Virtual Machine Manager no site primário e um servidor do Virtual Machine Manager no site secundário.<br/><br/> Você pode [replicar entre nuvens em um único servidor VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Para fazer isso, você precisa de pelo menos duas nuvens configuradas no servidor do Virtual Machine Manager.<br/><br/> Os servidores do Virtual Machine Manager devem estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor do Virtual Machine Manager deve ter pelo menos uma ou mais nuvens. Todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts do Virtual Machine Manager. Para mais informações sobre como configurar nuvens do Virtual Machine Manager, consulte [Preparar para a implantação do Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Os servidores Hyper-V devem executar, no mínimo, o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Os servidores host Hyper-V devem estar localizados em grupos de hosts nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, recomendamos que instale a [atualização 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012 e tiver um cluster baseado em endereço IP estático, o agente de cluster não será criado automaticamente. Você deve configurar o agente do cluster manualmente. Para mais informações sobre o agente de cluster, consulte [Configurar cluster de função de agente de réplica para replicação de cluster](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Provedor** | Durante a implantação do Site Recovery, instale o provedor Azure Site Recovery nos servidores do Virtual Machine Manager. O Provedor se comunica com a Recuperação de Site por HTTPS 443 para orquestrar a replicação. A replicação de dados ocorre entre os servidores Hyper-V primário e secundário sobre a LAN ou uma conexão VPN.<br/><br/> O provedor em execução no servidor do Virtual Machine Manager precisa de acesso a essas URLs:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>O provedor deve permitir a comunicação de firewall de servidores do Virtual Machine Manager para [Intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir o protocolo HTTPS (443). |


## <a name="url-access"></a>Acesso à URL
Essas URLs devem estar disponíveis no VMware, no VMM e nos servidores host Hyper-V.

|**URL** | **VMM no VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware no Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir |
|``*.backup.windowsazure.com`` | Não requerido | Permitir | Permitir | Permitir |
|``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir |
|``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir |
|``*.blob.core.windows.net`` | Não requerido | Permitir | Permitir | Permitir |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Não requerido | Não requerido | Não requerido | Permitir download do SQL |
|``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir|
|``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR |

