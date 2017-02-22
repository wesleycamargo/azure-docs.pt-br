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
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>Pré-requisitos para replicação no Azure usando o Azure Site Recovery

 A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você pode fazer failover em um local secundário a fim de manter aplicativos e cargas de trabalho disponíveis. Quando o local primário retomar as operações normais, você poderá realizar o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

Este artigo resume os pré-requisitos necessários para começar a replicação do Site Recovery no Azure.

Poste comentários no final do artigo ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Serviço do Site Recovery** | [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. |
**Armazenamento do Azure** | Você precisa de uma conta de armazenamento do Azure para armazenar os dados replicados e ela deve estar na mesma região que o cofre dos Serviços de Recuperação. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover.<br/><br/> Dependendo do modelo de recurso que deseja usar para as VMs do Azure com failover, você poderá configurar uma conta no [modelo do Resource Manager](../storage/storage-create-storage-account.md) ou no [modelo Clássico](../storage/storage-create-storage-account-classic-portal.md).<br></br>Você pode usar o armazenamento LRS ou [GRS](../storage/storage-redundancy.md#geo-redundant-storage). É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada.<br/><br/> Se você replicar VMs VMware ou servidores físicos no portal do Azure, haverá suporte para o armazenamento premium. O [armazenamento premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) normalmente é usado para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho com uso intensivo de E/S. Com o armazenamento premium para dados replicados, você também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.<br/><br/>
**Limitações de armazenamento** | Não é possível mover contas de armazenamento usadas pelo Site Recovery entre grupos de recursos das assinaturas.<br/><br/> No momento, não há suporte para replicação em contas de armazenamento premium na Índia Central e no Sul da Índia.
**Rede do Azure** | Você precisa de uma rede do Azure à qual as VMs do Azure se conectarão após o failover e ela deve estar na mesma região que o cofre dos Serviços de Recuperação.<br/><br/> No Portal do Azure, você pode criar redes no [modelo do Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modelo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Se você replicar do VMM para o Azure, você configurará o [mapeamento de rede](site-recovery-network-mapping.md) entre as redes de VM do VMM e as redes do Azure, para garantir que as VMs do Azure se conectem a redes apropriadas após o failover.
**Limitações de rede** | Não é possível mover contas de rede usadas pelo Site Recovery entre grupos de recursos das assinaturas.
**Mapeamento de rede** | Se você replicar VMs do Hyper-V em nuvens de VMM, será preciso configurar o [mapeamento de rede](site-recovery-network-mapping.md) para que as VMs do Azure sejam conectadas às redes apropriadas quando elas forem criadas após o failover.

>[!NOTE]
>As seções abaixo descrevem os pré-requisitos para vários componentes no ambiente do cliente. Para saber mais sobre o suporte para configurações específicas, leia a [matriz de suporte.](site-recovery-support-matrix.md)
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>Recuperação de Desastre de máquinas virtuais VMware ou servidores físicos Windows/Linux no Azure
Veja abaixo os componentes obrigatórios para a recuperação de desastre de máquinas virtuais VMware ou servidores físicos Windows/Linux além dos mencionados [acima.](#Azure requirements)


1. **Servidor de configuração ou servidor de processo adicional**: você precisará configurar um computador local como o servidor de configuração para coordenar a comunicação entre o site local e o Azure, bem como para gerenciar a replicação de dados. <br></br>

2. **Host VMware vCenter/vSphere**

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Um ou mais hipervisores do VMware vSphere.<br/><br/>Os hipervisores devem estar executando o vSphere versão 6.0, 5.5 ou 5.1 com as últimas atualizações.<br/><br/>Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede do servidor em processo (essa é a rede na qual o servidor de configuração está localizado, a menos que você configurou um servidor em processo dedicado). |
| **vCenter** | Recomendamos que você implante um servidor VMware vCenter para gerenciar os hosts vSphere. Ele deve executar o vCenter versão 6.0 ou 5.5, com as últimas atualizações.<br/><br/>**Limitação**: o Site Recovery não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento. O suporte à Recuperação de Site está limitado a recursos que também estavam disponíveis na versão 5.5.||

3.**Pré-requisitos de computadores replicados**


| **Componente** | **Requisitos** |
| --- | --- |
| **Local (VMs VMware)** | As VMs replicadas devem ter as ferramentas VMware instaladas e em execução.<br/><br/> Para que seja possível criar VMs do Azure, elas devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).<br/><br/>A capacidade do disco individual nos computadores protegidos não deve ser maior que 1023 GB. <br/><br/>Mínimo de 2 GB de espaço disponível na unidade de instalação para instalação de componentes.<br/><br/>A **porta 20004** deverá ser aberta no firewall local da VM se você desejar habilitar a coerência de múltiplas VMs.<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois de habilitar a replicação em um computador, você poderá modificar o nome do Azure.<br/><br/> |
| **Computadores Windows (físicos ou VMware)** | O computador deve estar executando um sistema operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com, ao menos, SP1.<br/><br/> O sistema operacional deve ser instalado na unidade C:\. O disco do sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/>|
| **Computadores com Linux** (físicos ou VMware) | Você precisa de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7, 7.1, 7.2; Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2; Oracle Enterprise Linux 6.4, 6.5, que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Os arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.<br/><br/>Se você quiser se conectar a uma máquina virtual do Azure que executa o Linux após o failover usando um cliente Secure Shell (ssh), verifique se o serviço do Secure Shell na máquina protegida está definido para ser iniciado automaticamente na inicialização do sistema e se as regras de firewall permitem uma conexão ssh para ele.<br/><br/>O nome do host, os pontos de montagem, os nomes de dispositivos e os caminhos do sistema e nomes de arquivos do Linux (por exemplo: /etc/; /usr) devem ser somente em inglês.<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>Recuperação de Desastre de máquinas virtuais do Hyper-V no Azure (sem VMM)

Veja abaixo os componentes obrigatórios para a recuperação de desastre de máquinas virtuais do Hyper-V em nuvens VMM além dos mencionados [acima.](#Azure requirements)

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Host Hyper-V** |Um ou mais servidores locais executando o Windows Server 2012 R2 com a função Hyper-V e as atualizações mais recentes habilitadas ou o Microsoft Hyper-V Server 2012 R2.<br></br>O servidor Hyper-V deve conter uma ou mais máquinas virtuais.<br></br>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br></br>Servidores Hyper-V devem ter correções mencionadas no [KB2961977](https://support.microsoft.com/en-us/kb/2961977) instalado.
|**Provedor e agente**| Durante a implantação do Azure Site Recovery, você instalará o provedor do Azure Site Recovery. A instalação do Provedor também instalará o Agente dos Serviços de Recuperação do Azure em cada servidor Hyper-V executando máquinas virtuais que deseja proteger. <br></br>Todos os servidores Hyper-V em um cofre de Recuperação de Site devem ter as mesmas versões do Provedor e do agente.<br></br>O Provedor precisará se conectar ao Azure Site Recovery pela Internet. O tráfego pode ser enviado diretamente ou por meio de um proxy. Observe que o proxy com base em HTTPS não é suportado. O servidor proxy deve permitir o acesso a: <br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure.<br></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Recuperação de Desastre de máquinas virtuais do Hyper-V em nuvem VMM no Azure

Veja abaixo os componentes obrigatórios para a recuperação de desastre de máquinas virtuais do Hyper-V em nuvens VMM além dos mencionados [acima.](#Azure requirements)

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **VMM** |Um ou mais servidores VMM em execução no **System Center 2012 R2 ou superior**. Cada servidor do VMM deve ter uma ou mais nuvens configuradas. <br></br>Uma nuvem deve conter<br>- Um ou mais grupos de hosts do VMM.<br/>- Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.<br/><br/>[Saiba mais](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) sobre a configuração de nuvens do VMM. |
| **Hyper-V** |Os servidores host do Hyper-V devem estar executando pelo menos o **Windows Server 2012 R2** com a função Hyper-V ou o **Microsoft Hyper-V Server 2012 R2** e ter as atualizações mais recentes instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/> Um servidor de host Hyper-V ou cluster que inclui VMs as quais você deseja replicar deve ser gerenciado em uma nuvem VMM.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Os servidores Hyper-V devem ter as correções mencionadas no [artigo&29619;77](https://support.microsoft.com/kb/2961977) instaladas.<br/><br/>Os servidores host do Hyper-V precisam de acesso à Internet para a replicação de dados no Azure. |
| **Provedor e agente** |Durante a implantação do Azure Site Recovery, você instala o Provedor do Azure Site Recovery no servidor do VMM e no agente dos Serviços de Recuperação em hosts do Hyper-V. O Provedor e o agente precisam se conectar ao Azure pela Internet diretamente ou por meio de um proxy. Não há suporte para um proxy com base em HTTPS. O servidor proxy no servidor do VMM e hosts Hyper-V devem permitir o acesso a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor VMM, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/><br/> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de computadores replicados
| **Componente** | **Detalhes** |
| --- | --- |
| **VMs protegidas** | O Site Recovery é compatível com todos os sistemas operacionais que são aceitos pelo [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br></br>Para que seja possível criar VMs do Azure, elas devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. <br></br>Você pode modificar o nome depois de habilitar a replicação para a VM. <br/><br/> A capacidade de disco individual em computadores protegidos não deve ser superior a 1023 GB. Uma VM pode ter até 16 discos (portanto, até 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>Recuperação de Desastre de máquinas virtuais do Hyper-V em nuvens VMM em um site de propriedade d cliente

Veja abaixo os componentes obrigatórios para a recuperação de desastre de máquinas virtuais do Hyper-V em nuvens VMM em um site de propriedade do cliente além dos mencionados [acima.](#Azure requirements)

| **Componentes** | **Detalhes** |
| --- | --- |
| **VMM** |  Recomendamos a implantação com um servidor VMM no site primário e de um servidor VMM no site secundário.<br/><br/> Você pode [replicar entre nuvens em um único servidor VMM](site-recovery-single-vmm.md). Para fazer isso, você precisará de pelo menos duas nuvens configuradas no servidor VMM.<br/><br/> Os servidores VMM devem estar executando **pelo menos o System Center 2012 SP1** com as atualizações mais recentes.<br/><br/> Cada servidor VMM deve ter pelo menos uma ou mais nuvens. Todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts do VMM. [Saiba mais](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) sobre a configuração de nuvens do VMM.<br/><br/> Servidores VMM precisam de acesso à Internet. |
| **Hyper-V** | Os servidores Hyper-V devem estar executando **pelo menos o Windows Server 2012 com a função Hyper-V** e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Os servidores host Hyper-V devem estar localizados em grupos de hosts nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, instale a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012, observe que o agente de cluster não será criado automaticamente se você tiver um cluster baseado em endereço IP estático. Você precisará configurar o agente de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Provedor** | Durante a implantação do Site Recovery, você instala o Provedor do Azure Site Recovery em servidores VMM. O Provedor se comunica com o Site Recovery em HTTPS 443 para orquestrar a replicação. A replicação de dados ocorre entre os servidores Hyper-V primário e secundário na LAN ou em uma conexão VPN.<br/><br/> O Provedor em execução no servidor VMM precisa de acesso a estas URLs:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Permita a comunicação de firewall dos servidores VMM nos [Intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permita o protocolo HTTPS (443). |



<!--HONumber=Jan17_HO2-->


