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
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 511e61552ec0a2c862ebd99fd881f87b56b69398
ms.openlocfilehash: 84eabb9c898d32efeb6e1c4b1f17b683940b4c04
ms.lasthandoff: 02/22/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicação no Azure

> [!div class="op_single_selector"]
> * [Replicar para o Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicar no site secundário possuído pelo cliente](site-recovery-support-matrix-to-sec-site.md)


Este artigo resume os componentes e as configurações com suporte do Azure Site Recovery durante a replicação e recuperação no Azure. Para saber mais sobre os requisitos do Azure Site Recovery, confira os [pré-requisitos](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Suporte para opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
**Portal do Azure** | VMs VMware locais para armazenamento do Azure, com redes e armazenamento clássicos ou do Azure Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager. | VMs Hyper-V locais (não em nuvens do Virtual Machine Manager) para armazenamento do Azure, com redes e armazenamento clássicos ou do Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager. | Hyper-V local (VMs em nuvens do Virtual Machine Manager) para armazenamento do Azure, com redes e armazenamento clássicos ou do Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção. | Somente modo de manutenção.
**PowerShell** | Não há suporte no momento. | Suportado | Suportado


## <a name="support-for-datacenter-management-servers"></a>Suporte para servidores de gerenciamento do datacenter

### <a name="virtualization-management-entities"></a>Entidades de gerenciamento de virtualização

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vSphere 6.0, 5.5 ou 5.1 com a atualização mais recente
**Hyper-V (com Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > No momento, não há suporte para uma nuvem do System Center Virtual Machine Manager 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.

### <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5) 
**Hyper-V (sem Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes
**Hyper-V (com Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes.<br/><br/> Os hosts do Windows Server 2016 devem ser gerenciados pelo System Center Virtual Machine Manager 2016.

  >[!Note]
  >No momento, não há suporte para um site do Hyper-V que combine hosts que executam o Windows Server 2016 e 2012 R2.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

As máquinas virtuais protegidas devem atender aos [requisitos do Azure](#failed-over-azure-vm-requirements) ao replicar no Azure.
A tabela a seguir resume o suporte a sistemas operacionais replicados em vários cenários de implantação durante o uso do Azure Site Recovery. Esse suporte é aplicável a qualquer carga de trabalho em execução no SO mencionado.

 **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx) | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!Note]
>Suporte ao armazenamento para sistema de arquivos nas versões do Linux (EXT3, ETX4, ReiserFS, XFS), Mapeador de Dispositivo por software Multipath, Gerenciador de volumes (LVM2) e servidores físicos com o armazenamento do controlador HP CCISS *não* têm suporte.
>O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network-configuration"></a>Suporte para configuração da rede
As tabelas a seguir resumem o suporte à configuração de rede em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-network-configuration"></a>Configuração de rede do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Agrupamento NIC | Sim<br/><br/>Sem suporte em computadores físicos| Sim | Sim
VLAN | Sim | Sim | Sim
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não

### <a name="guest-vm-network-configuration"></a>Configuração de rede da VM convidada

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Agrupamento NIC | Não | Não | Não
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não
IP estático (Windows) | Sim | Sim | Sim
IP estático (Linux) | Não | Não | Não
NIC múltipla | Sim | Sim | Sim

### <a name="failed-over-azure-vm-network-configuration"></a>Configuração de rede da VM do Azure após failover

**Rede do Azure** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Rota Expressa | Sim | Sim | Sim
ILB | Sim | Sim | Sim
ELB | Sim | Sim | Sim
Gerenciador de Tráfego | Sim | Sim | Sim
NIC múltipla | Sim | Sim | Sim
IP Reservado | Sim | Sim | Sim
IPv4 | Sim | Sim | Sim
Reter o IP de origem | Sim | Sim | Sim


## <a name="support-for-storage"></a>Suporte para armazenamento
As tabelas a seguir resumem o suporte à configuração de armazenamento em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | N/D | N/D
SMB 3.0 | N/D | Sim | Sim
SAN (ISCSI) | Sim | Sim | Sim
Múltiplos caminhos (MPIO)<br></br>Testado com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | Sim | Sim | Sim

### <a name="guest-or-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico ou convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
VMDK | Sim | N/D | N/D
VHD/VHDX | N/D | Sim | Sim
VM ger 2 | N/D | Sim | Sim
EFI/UEFI| Não | Sim | Sim
Disco de cluster compartilhado | Sim para VMware<br/><br/> N/D para servidores físicos | Não | Não
Disco criptografado | Não | Não | Não
NFS | Não | N/D | N/D
SMB 3.0 | Não | Não | Não
RDM | Sim<br/><br/> N/D para servidores físicos | N/D | N/D
Disco > 1 TB | Não | Não | Não
Volume com discos distribuídos > 1 TB<br/><br/> Gerenciamento de Volume lógico LVM | Sim | Sim | Sim
Espaços de Armazenamento | Não | Sim | Sim
Adição/remoção de disco a quente | Não | Não | Não
Exclusão de disco | Sim | Sim | Sim
Múltiplos caminhos (MPIO) | N/D | Sim | Sim

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
LRS | Sim | Sim | Sim
GRS | Sim | Sim | Sim
Armazenamento frio | Não | Não | Não
Armazenamento quente| Não | Não | Não
Criptografia em repouso (SSE)| Sim | Sim | Sim
Armazenamento Premium | Sim | Não | Não
Serviço de importação/exportação | Não | Não | Não


## <a name="support-for-azure-compute-configuration"></a>Suporte para configuração de computação do Azure

**Recurso de computação** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Conjuntos de disponibilidade | Não | Não | Não
HUB | Sim | Sim | Sim

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de VM do Azure após failover

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. As VMs locais que você deseja replicar devem estar em conformidade com os requisitos do Azure a seguir durante a replicação no Azure.

**Entidade** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 64 ou menos se você estiver replicando **VMs do VMware no Azure**; 16 ou menos se você estiver replicando **VMs do Hyper-V no Azure** | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
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
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não


## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o Agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores locais do Virtual Machine Manager ou em servidores Hyper-V, se não houver nenhum servidor do Virtual Machine Manager | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.3.4246.1 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | N/D (disponível no portal) | N/D
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado em servidores Hyper-V locais (com ou sem um servidor do Virtual Machine Manager) | Agente mais recente ([disponível no portal](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Próximas etapas
[Verificar pré-requisitos](site-recovery-prereq.md)

