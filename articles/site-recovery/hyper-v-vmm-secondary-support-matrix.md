---
title: Matriz de suporte para recuperação de desastre de VMs do Hyper-V em nuvens do VMM para um site secundário com o Azure Site Recovery | Microsoft Docs
description: Resume o suporte para a replicação de VM do Hyper-V em nuvens VMM para um site secundário com Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2018
ms.author: raynew
ms.openlocfilehash: 419974138d6f556a2f774e8c1a355729c824fddc
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310601"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastres de VMs do Hyper-V para um site secundário

Este artigo resume o que terá suporte quando você utilizar o serviço do [Azure Site Recovery](site-recovery-overview.md) para replicar VMs do Hyper-V gerenciadas em VMM (System Center Virtual Machine Manager) em um site secundário. Se você quer replicar VMs do Hyper-V para o Azure, analise [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Só é possível replicar para um site secundário quando os hosts do Hyper-V são gerenciados em nuvens VMM.

  

## <a name="host-servers"></a>Servidores de host

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2012 R2 | Os servidores devem estar executando as últimas atualizações.
Windows Server 2016 |  No momento, não há suporte para nuvens VMM 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.<br/><br/> Atualmente, não há suporte para implantações atualizadas do System Center 2012 R2 VMM 2012 R2 para System Center 2016.


## <a name="replicated-vm-support"></a>Suporte de VM replicada

A tabela a seguir resume o suporte de sistema operacional para computadores replicados com o Site Recovery. Qualquer carga de trabalho pode ser executada no sistema operacional com suporte.

**Versão do Windows** | **Hyper-V (com o VMM)**
--- | ---
Windows Server 2016 | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) no Windows Server 2016 
Windows Server 2012 R2 | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) no Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Armazenamento de computador do Linux

Somente computadores Linux com o armazenamento a seguir podem ser replicados:

- Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS).
- Software Multipath – Mapeador de dispositivos.
- Gerenciador de volumes (LVM2).
- Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS.
- O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração de rede - VM Host/Convidada

**Configuração** | **Com suporte**  
--- | --- 
Host - Agrupamento NIC | Sim 
Host - VLAN | Sim 
Host - IPv4 | Sim 
Host - IPv6 | Não 
VM Convidada - Agrupamento NIC | Não
VM Convidada - IPv4 | Sim
VM Convidada - IPv6 | Não
VM convidada – Windows/Linux – Endereço IP estático | Sim
VM Convidada - Multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Com suporte**
--- | --- 
NFS | Não aplicável
SMB 3.0 |  Sim
SAN (ISCSI) | Sim
Múltiplos caminhos (MPIO) | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento do servidor físico ou convidado

**Configuração** | **Com suporte**
--- | --- | 
VMDK |  Não aplicável
VHD/VHDX | Sim (até 16 discos)
VM ger 2 | Sim
Disco de cluster compartilhado | Não
Disco criptografado | Não
UEFI| Não aplicável
NFS | Não
SMB 3.0 | Não
RDM | Não aplicável
Disco > 1 TB | Sim
Volume com discos distribuídos > 1 TB<br/><br/> LVM | Sim
Espaços de Armazenamento | Sim
Adição/remoção de disco a quente | Não
Exclusão de disco | Sim
Múltiplos caminhos (MPIO) | Sim

## <a name="vaults"></a>Cofres

**Ação** | **Com suporte**
--- | --- 
Mover cofres entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) |  Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não

## <a name="azure-site-recovery-provider"></a>Provedor do Azure Site Recovery

O Provedor coordena as comunicações entre os servidores VMM. 

**Mais recente** | **Atualizações**
--- | --- 
5.1.19 ([(disponível no portal](https://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Próximas etapas

[Replicar as VMs do Hyper-V em nuvens de VMM para um site secundário](tutorial-vmm-to-vmm.md)

