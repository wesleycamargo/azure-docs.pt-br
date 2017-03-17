---
title: Matriz de suporte do Azure Site Recovery | Microsoft Docs
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b9d269a780e9a4c61263208f26f440b1121c682
ms.openlocfilehash: f437598b612a145c5dd8b46a1ba340d298a76981
ms.lasthandoff: 03/01/2017


---
# <a name="azure-site-recovery-support-matrix"></a>Matriz de suporte do Azure Site Recovery

Este artigo resume os sistemas operacionais e componentes com suporte para do Azure Site Recovery. Uma lista de componentes e pré-requisitos com suporte está disponível para cada cenário de implantação em cada artigo de implantação correspondente, e este documento os resume.

## <a name="support-for-azure-replication-scenarios"></a>Suporte para cenários de replicação do Azure

**Implantação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
**Portal do Azure** | VMs do VMware locais para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | VMs do Hyper-V locais (e não nuvens de VMM) para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | VMs do Hyper-V locais em nuvens de MM para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção. | Somente modo de manutenção.
**PowerShell** | Não há suporte no momento. | Suportado | Suportado


## <a name="support-for-secondary-site-replication-scenarios"></a>Suporte para cenários de replicação do site secundário

**Implantação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
**Portal do Azure** | VMs do VMware locais para o site secundário do VMware.<br/><br/> Baixe o guia de ajuda](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o Guia de ajuda do InMage Scout. Não disponível no Portal do Azure | Sem suporte | VMs do Hyper-V locais em nuvens de VMM para uma nuvem de VMM secundária<br/><br/> Somente Replicação Hyper-V Standard, sem suporte a SAN
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção. | Somente modo de manutenção.
**PowerShell** | Sem suporte. | ND | Suportado



## <a name="support-for-virtualization-server-operating-systems"></a>Suporte para sistemas operacionais com servidores de virtualização

### <a name="host-servers-replicate-to-azure"></a>Servidores host (replicação para o Azure)

**Servidor da VM VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | ---
vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5)  <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes<br/><br/> No momento, não há suporte para um site do Hyper-V que combine hosts que executam o Windows Server 2016 e 2012 R2. | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes<br/><br/> Hosts do Windows Server 2016 devem ser gerenciados pelo VMM que executa o System Center 2016.<br/><br/> No momento, não há suporte para uma nuvem VMM 2016 com uma combinação de hosts Windows Server 2016 e 2012 R2.

### <a name="host-servers-replicate-to-secondary-site"></a>Servidores host (replicação para o site secundário)

**Servidor da VM VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5)  <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes | Windows Server 2016, Windows Server 2012 R2 ou Windows Server 2012 com as atualizações mais recentes.<br/><br/> Hosts do Windows Server 2016 devem ser gerenciados pelo VMM que executa o System Center 2016.<br/><br/> No momento, não há suporte para uma nuvem VMM 2016 com uma combinação de hosts Windows Server 2016 e hosts mais antigos.


## <a name="support-for-replicated-machines"></a>Suporte para computadores replicados

### <a name="machines-replicate-to-azure"></a>Computadores (replicação para o Azure)

As máquinas virtuais devem atender os [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

**Requisito** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
O que é replicado | Qualquer carga de trabalho em uma VM do Windows ou do Linux | Qualquer carga de trabalho | Qualquer carga de trabalho
SO host | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 e 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3. | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx) | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)


### <a name="machines-replicate-to-secondary-site"></a>Computadores (replicação para o site secundário)

**Requisito** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
O que é replicado | Qualquer carga de trabalho em uma VM do Windows ou do Linux | Qualquer carga de trabalho | Qualquer carga de trabalho
SO host | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 e 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2).<br/><br/> Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3. | Qualquer SO convidado com suporte no Hyper-V] (https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure/site secundário  <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver nenhum servidor VMM | 5.1.1700 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.3.4246.1 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | NA (disponível no portal) | .
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado nos servidores Hyper-V locais (com ou sem um servidor VMM) | |

## <a name="support-for-networking"></a>Suporte para rede

### <a name="networking-replicate-to-azure"></a>Rede (replicação para o Azure)

**Rede host** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Agrupamento NIC | Sim<br/><br/>Sem suporte em computadores físicos| Sim | Sim
VLAN | Sim | Sim | Sim
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não

**Rede de VM convidada** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Agrupamento NIC | Não | Não | Não
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não
IP estático (Windows) | Sim | Sim | Sim
IP estático (Linux) | Não | Não | Não
NIC múltipla | Sim | Sim | Sim

**Rede do Azure** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Rota Expressa | Sim | Sim | Sim
ILB | Sim | Sim | Sim
ELB | Sim |  |
Gerenciador de Tráfego | Sim | Sim | Sim
NIC múltipla | Sim | Sim | Sim
IP Reservado | Sim | Sim | Sim
IPv4 | Sim | Sim | Sim
Reter o IP de origem | Sim | Sim | Sim

### <a name="networking-replicate-to-secondary-site"></a>Rede (replicação para o site secundário)

**Rede host** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Sim | Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

**Rede de VM convidada** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim | Sim
NIC múltipla | Sim | Sim


## <a name="support-for-storage"></a>Suporte para armazenamento

### <a name="storage-replicate-to-azure"></a>Armazenamento (replicação para o Azure)

**Armazenamento (host)** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | ND | ND
SMB 3.0 | ND | Sim | Sim
SAN (ISCSI) | Sim | Sim | Sim
Múltiplos caminhos (MPIO) | Sim para VMware<br/><br/> ND para servidores físicos | Sim | Sim

**Armazenamento (servidor da VM convidada/físico)** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
VMDK | Sim | ND | ND
VHD/VHDX | ND | Sim | Sim
VM ger 2 | ND | Sim | Sim
Disco de cluster compartilhado | Sim para VMware<br/><br/> ND para servidores físicos | Não | Não
Disco criptografado | Não | Não | Não
EFI/UEFI| Não | Sim | Sim
NFS | Não | Não | Não
SMB 3.0 | Não | Não | Não
RDM | Sim<br/><br/> ND para servidores físicos | ND | ND
Disco > 1 TB | Não | Não | Não
Volume com discos distribuídos > 1 TB<br/><br/> LVM | Sim | Sim | Sim
Espaços de Armazenamento | Não | Sim | Sim
Adição/remoção de disco a quente | Não | Não | Não
Exclusão de disco | Sim | Sim | Sim
Múltiplos caminhos (MPIO) | ND | Sim | Sim

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
LRS | Sim | Sim | Sim
GRS (somente para o armazenamento padrão) | Sim | Sim | Sim
Armazenamento frio | Não | Não | Não
Armazenamento quente| Não | Não | Não
Criptografia em repouso | Sim | Sim | Sim
Armazenamento Premium | Sim | Sim | Sim
Serviço de importação/exportação | Não | Não | Não


### <a name="storage-replicate-to-secondary-site"></a>Armazenamento (replicação para o site secundário)

**Armazenamento (host)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
NFS | Sim | ND
SMB 3.0 | ND | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (MPIO) | Sim | Sim

**Armazenamento (servidor da VM convidada/físico)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
VMDK | Sim | ND
VHD/VHDX | ND | Sim (até 16 discos)
VM ger 2 | ND | Sim
Disco de cluster compartilhado | Sim  | Não
Disco criptografado | Não | Não
UEFI| Não | ND
NFS | Não | Não
SMB 3.0 | Não | Não
RDM | Sim | ND
Disco > 1 TB | Não | Sim
Volume com discos distribuídos > 1 TB<br/><br/> LVM | Sim | Sim
Espaços de Armazenamento | Não | Sim
Adição/remoção de disco a quente | Não | Não
Exclusão de disco | Não | Não
Múltiplos caminhos (MPIO) | ND | Sim

## <a name="support-for-recovery-services-vault-actions"></a>Suporte para ações do cofre dos Serviços de Recuperação

### <a name="vaults-replicate-to-azure"></a>Cofres (replicação para o Azure)

**Ação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não

### <a name="vaults-replicate-to-secondary-site"></a>Cofres (replicação para o site secundário)

**Ação** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não


## <a name="support-for-azure-compute-replicate-to-azure"></a>Suporte para computação do Azure (replicação para o Azure)

**Recurso de computação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Clusters convidados de disco compartilhado | Não | Não | Não
Conjuntos de disponibilidade | Não | Não | Não
HUB | Sim | Sim | Sim

## <a name="support-for-azure-vms"></a>Suporte para VMs do Azure

Você pode implantar o Site Recovery para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte no Azure. Isso inclui a maioria das versões do Windows e do Linux. As VMs locais que você deseja replicar deve estar em conformidade com os requisitos do Azure.

**Recurso** | **Requisitos** | **Detalhes**
--- | --- | ---
**Host Hyper-V** | Deve executar o Windows Server 2012 R2 ou posterior | A verificação de pré-requisitos falhará se o sistema operacional não tiver suporte
**Hipervisor VMware** | Sistema operacional com suporte | [Verifique os requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 64 ou menos se você estiver replicando **VMs do VMware no Azure**; 16 ou menos se você estiver replicando **VMs do Hyper-V no Azure** | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Adaptadores de rede** | Há suporte para vários adaptadores |
**Endereço IP estático** | Suportado | Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure.<br/><br/> Não há suporte para o endereço IP estático em uma **VM do Linux em execução no Hyper-V**.
**Disco iSCSI** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | As VMs da Geração 2 com um tipo de disco básico de sistema operacional, que inclui um ou dois volumes de dados formatados como VHDX e há suporte para menos de 300 GB.<br/><br/>. Não há suporte para VMs do Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>Próximas etapas
Verificar [pré-requisitos](site-recovery-prereq.md)

