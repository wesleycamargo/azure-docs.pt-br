---
title: "Matriz de suporte do Azure Site Recovery para replicação no Azure | Microsoft Docs"
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rajanaki
ms.openlocfilehash: a72c9104dc2df0c8a874f757c100a19dc26c1564
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicação do local para o Azure


Este artigo resume os componentes e as configurações com suporte do Azure Site Recovery durante a replicação e recuperação no Azure. Para saber mais sobre os requisitos do Azure Site Recovery, confira os [pré-requisitos](site-recovery-prereq.md).

> [!NOTE]
> Atualize para a versão mais recente do provedor e do agente de Site Recovery para ter compatibilidade com as atualizações na matriz de suporte.


## <a name="support-for-deployment-options"></a>Suporte para opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)** |
--- | --- | ---
**Portal do Azure** | VMs VMware locais para armazenamento do Azure, com redes e armazenamento clássicos ou do Azure Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager. | VMs do Hyper-V locais para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção.
**PowerShell** | Com suporte | Com suporte


## <a name="support-for-datacenter-management-servers"></a>Suporte para servidores de gerenciamento do datacenter

### <a name="virtualization-management-entities"></a>Entidades de gerenciamento de virtualização

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 6.5, 6.0 ou 5.5
**Hyper-V (com Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > No momento, não há suporte para uma nuvem do System Center Virtual Machine Manager 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.
  > As configurações que incluem o upgrade de um SCVMM 2012 R2 existente para 2016 não são suportadas atualmente.
### <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vSphere 6.5, 6.0, 5.5
**Hyper-V (com/sem o Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes.<br></br>Se for usado o SCVMM, os hosts do Windows Server 2016 deverão ser gerenciados pelo SCVMM 2016.


  >[!Note]
  >No momento, não há suporte para um site do Hyper-V que combine hosts que executam o Windows Server 2016 e 2012 R2. Atualmente, não há suporte para recuperação de VMs em um host do Windows Server 2016 para um local alternativo.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

As máquinas virtuais protegidas devem atender aos [requisitos do Azure](#failed-over-azure-vm-requirements) ao replicar no Azure.
A tabela a seguir resume o suporte a sistemas operacionais replicados em vários cenários de implantação durante o uso do Azure Site Recovery. Esse suporte é aplicável a qualquer carga de trabalho em execução no SO mencionado.

 **Servidor VMware/físico** | **Hyper-V (com/sem o VMM)** |
--- | --- |
Windows Server 2016 de 64 bits (Server Core, Server com Desktop Experience)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4<br/><br/>CentOS: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4 <br/><br/>Servidor do Ubuntu 14.04 LTS[ (suporte para versões de kernel)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Servidor LTS do Ubuntu 16.04[ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Não há suporte para a atualização de computadores de replicação de SLES 11 SP3 até SLES 11 SP4. Se um computador replicado tiver sido atualizado do SLES 11 SP3 para o SLES 11 SP4, você precisará desabilitar a replicação e proteger o computador novamente após a atualização). | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \* Não há suporte para o Windows Server 2016 Nano Server.
>
> Em distribuições Linux, há suporte somente para os kernels de estoque que fazem parte da versão/atualização da versão secundária da distribuição.
>
> Atualizações de versões principais de uma distribuição Linux em uma máquina virtual do VMware protegida pelo Azure Site Recovery ou em um servidor físico não são compatíveis. Durante a atualização do sistema operacional em versões principais (por exemplo, CentOS 6.* para CentOS 7.*), desabilite a replicação para a máquina, atualize o sistema operacional nela, em seguida, habilite a replicação novamente.
> 


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versões com suporte do kernel Ubuntu para servidores VMware/físicos

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic a 3.13.0-132-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic a 3.13.0-137-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic a 4.4.0-96-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic a 4.4.0-104-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Sistemas de arquivos e configurações de armazenamento de convidado com suporte no Linux (servidores VMware/físicos)

Os seguintes sistemas de arquivos e software de configuração de armazenamento são compatíveis com servidores Linux em execução nos servidores VMware ou físicos:
* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
* Gerenciador de volumes: LVM2
* Software Multipath: Mapeador de dispositivos

Não há suporte para dispositivos de armazenamento paravirtualizados (dispositivos exportados por drivers paravirtualizados).<br/>
Não há suporte para dispositivos de E/S de bloco de várias filas.<br/>
Não há suporte para servidores físicos com o controlador de armazenamento CCISS da HP.<br/>

>[!Note]
> Em servidores Linux, os diretórios a seguir (se configurados como partições/sistemas de arquivos separados) devem estar no mesmo disco (disco do sistema operacional) no servidor de origem: / (raiz), /boot, /usr, /usr/local, /var e /etc; e /boot deve estar em uma partição do disco em vez de ser um volume de LVM<br/><br/>
>


## <a name="support-for-network-configuration"></a>Suporte para configuração da rede
As tabelas a seguir resumem o suporte à configuração de rede em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-network-configuration"></a>Configuração de rede do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | sim<br/><br/>Não há suporte quando computadores físicos são replicados| sim
VLAN | sim | sim
IPv4 | sim | sim
IPv6 | Não  | Não 

### <a name="guest-vm-network-configuration"></a>Configuração de rede da VM convidada

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | Não  | Não 
IPv4 | sim | sim
IPv6 | Não  | Não 
IP estático (Windows) | sim | sim
IP estático (Linux) | sim <br/><br/>Máquinas virtuais são configuradas para usar DHCP no failback  | Não 
NIC múltipla | sim | sim

### <a name="failed-over-azure-vm-network-configuration"></a>Configuração de rede da VM do Azure após failover

**Rede do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | sim | sim
ILB | sim | sim
ELB | sim | sim
Gerenciador de Tráfego | sim | sim
NIC múltipla | sim | sim
IP Reservado | sim | sim
IPv4 | sim | sim
Reter o IP de origem | sim | sim
Pontos de Extremidade de Serviço de Rede Virtual (Redes virtuais e firewalls de Armazenamento do Azure) | Não  | Não 


## <a name="support-for-storage"></a>Suporte para armazenamento
As tabelas a seguir resumem o suporte à configuração de armazenamento em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | N/D
SMB 3.0 | N/D | sim
SAN (ISCSI) | sim | sim
Múltiplos caminhos (MPIO)<br></br>Testado com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | sim | sim

### <a name="guest-or-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico ou convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
VMDK | sim | N/D
VHD/VHDX | N/D | sim
VM ger 2 | N/D | sim
EFI/UEFI| Migração para o Azure somente para Windows Server 2012 e posterior. </br></br> ** Consulte a observação ao final da tabela.  | sim
Disco de cluster compartilhado | Não  | Não 
Disco criptografado | Não  | Não 
NFS | Não  | N/D
SMB 3.0 | Não  | Não 
RDM | sim<br/><br/> N/D para servidores físicos | N/D
Disco > 1 TB | sim<br/><br/>Até 4.095 GB | sim<br/><br/>Até 4.095 GB
Disco com tamanho de setor lógico de 4K e físico de 4K | sim | Não há suporte para VMs da Geração 1<br/><br/>Não há suporte para VMs da geração 2.
Disco com tamanho de setor lógico de 4K e físico de 512 bytes | sim |  sim
Volume com discos distribuídos > 1 TB<br/><br/> Gerenciamento de Volume lógico LVM | sim | sim
Espaços de Armazenamento | Não  | sim
Adição/remoção de disco a quente | Não  | Não 
Exclusão de disco | sim | sim
Múltiplos caminhos (MPIO) | N/D | sim

> [!NOTE]
> ** Máquinas virtuais da VMware com inicialização UEFI ou Servidores físicos com Windows Server 2012 ou posterior podem ser migrados para o Azure. As restrições a seguir se aplicam.
> - Migração somente para o Azure. O failback não é compatível com o site do VMware local.
> - Há suporte para, no máximo, 4 partições no disco do sistema operacional do servidor.
> - Exige o Serviço de mobilidade do Azure Site Recovery versão 9.13 ou posterior.

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
LRS | sim | sim
GRS | sim | sim
RA-GRS | sim | sim
Armazenamento frio | Não  | Não 
Armazenamento quente| Não  | Não 
Blobs de blocos | Não  | Não 
Criptografia em repouso (SSE)| sim | sim
Armazenamento Premium | sim | sim
Serviço de importação/exportação | Não  | Não 
Pontos de extremidade de serviço de rede virtual (Redes virtuais e firewall de armazenamento do Azure) configurados na conta de armazenamento de destino ou na conta de armazenamento de cache utilizada para armazenar dados de replicação | Não  | Não 
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Não  | Não 


## <a name="support-for-azure-compute-configuration"></a>Suporte para configuração de computação do Azure

**Recurso de computação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Conjuntos de disponibilidade | sim | sim
HUB | sim | sim  
Discos gerenciados | sim | sim<br/><br/>No momento, não há suporte para failback para o local da VM do Azure com discos gerenciados.

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de VM do Azure após failover

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. As VMs locais que você deseja replicar devem estar em conformidade com os requisitos do Azure a seguir durante a replicação no Azure.

**Entidade** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 2.048 GB se você estiver replicando **VMs VMware ou servidores físicos para o Azure**.<br/><br/>Até 2.048 GB para VMs **Hyper-V Geração 1**.<br/><br/>Até 300 GB para VMs **Hyper-V Geração 2**.  | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 64 ou menos se você estiver replicando **VMs do VMware no Azure**; 16 ou menos se você estiver replicando **VMs do Hyper-V no Azure** | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 4.095 GB | A verificação de pré-requisitos falhará se não houver suporte
**Adaptadores de rede** | Há suporte para vários adaptadores |
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. O nome da VM deve começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da máquina virtual no Site Recovery.
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | VMs da Geração 2 com um tipo de disco básico de SO (que inclui um ou dois volumes de dados formatados como VHDX) e suporte para menos de 300 GB de espaço em disco.<br></br>Não há suporte para VMs Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Suporte para ações do cofre dos Serviços de Recuperação

**Ação** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  | Não  | Não 
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  | Não  | Não 


## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o Agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores locais do Virtual Machine Manager ou em servidores Hyper-V, se não houver nenhum servidor do Virtual Machine Manager | 5.1.2700.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado em servidores Hyper-V locais (com ou sem um servidor do Virtual Machine Manager) | Agente mais recente (disponível no portal) |






## <a name="next-steps"></a>Próximas etapas
[Verificar pré-requisitos](site-recovery-prereq.md)
