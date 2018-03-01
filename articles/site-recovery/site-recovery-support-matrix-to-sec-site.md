---
title: "Suporte à matriz para replicação em um site secundário com o Azure Site Recovery | Microsoft Docs"
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: 256bad0c3c06182b6be2b647ae27db90fe69724d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Suporte a matriz para replicação em um site secundário com o Azure Site Recovery

Este artigo resume o que tem suporte quando você usa o serviço do [Azure Site Recovery](site-recovery-overview.md) para replicar para um site local secundário.

## <a name="supported-scenarios"></a>Cenários com suporte

**Implantação** | **Detalhes** 
--- | ---
**VMware para VMware** | Recuperação de desastre de VMs do VMware locais para o site secundário do VMware.<br/><br/> Baixe o [guia do usuário do InMage Scout](https://aka.ms/asr-scout-user-guide)
**Hyper-V para Hyper-V** | Recuperação de desastre de VMs do Hyper-V locais em nuvens de VMM para uma nuvem de VMM secundária.<br></br> Não há suporte sem o VMM.



  

## <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 5.5, 6.0 ou 6.5 (suporte somente a recursos do 5.5)
**Hyper-V com o VMM** | Windows Server 2016 e Windows Server 2012 R2 com as atualizações mais recentes.<br/><br/> Os hosts do Windows Server 2016 devem ser gerenciados pelo VMM 2016.<br/><br/> No momento, não há suporte para nuvens VMM 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.<br/><br/> Atualmente não há suporte para implantação que inclui uma atualização de um VMM 2012 R2 existente para o System Center 2016.


## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

A tabela a seguir resume o suporte de sistema operacional para computadores replicados com o Site Recovery. Qualquer carga de trabalho pode ser executada no sistema operacional com suporte.

**Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | ---
Windows Server 2016 R2 de 64 bits, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 que executa Red Hat compatível com kernel ou o Unbreakable Enterprise Kernel Versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Armazenamento de computador do Linux

Somente computadores Linux com o armazenamento a seguir podem ser replicados:

- Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS).
- Software Multipath – Mapeador de dispositivos.
- Gerenciador de volumes (LVM2).
- Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS.
- O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Configuração de rede

### <a name="hosts"></a>Hosts

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | sim | sim
VLAN | sim | sim
IPv4 | sim | sim
IPv6 | Não  | Não 

### <a name="guest-vms"></a>VMs convidadas

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Não  | Não 
IPv4 | sim | sim
IPv6 | Não  | Não 
IP estático (Windows) | sim | sim
IP estático (Linux) | sim | sim
NIC múltipla | sim | sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
NFS | sim | N/D
SMB 3.0 | N/D | sim
SAN (ISCSI) | sim | sim
Múltiplos caminhos (MPIO) | sim | sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento do servidor físico ou convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
VMDK | sim | N/D
VHD/VHDX | N/D | Sim (até 16 discos)
VM ger 2 | N/D | sim
Disco de cluster compartilhado | sim  | Não 
Disco criptografado | Não  | Não 
UEFI| sim | N/D
NFS | Não  | Não 
SMB 3.0 | Não  | Não 
RDM | sim | N/D
Disco > 1 TB | sim | sim
Volume com discos distribuídos > 1 TB<br/><br/> LVM | sim | sim
Espaços de Armazenamento | Não  | sim
Adição/remoção de disco a quente | sim | Não 
Exclusão de disco | sim | sim
Múltiplos caminhos (MPIO) | N/D | sim

## <a name="vaults"></a>Cofres

**Ação** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Mover cofres entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não  | Não 
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não  | Não 

## <a name="provider-and-agent"></a>Provedor e agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver nenhum servidor VMM | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware locais ou servidores físicos e o site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | N/D (disponível no portal) | N/D


## <a name="next-steps"></a>Próximas etapas

- [Replicar as VMs do Hyper-V em nuvens de VMM para um site secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
