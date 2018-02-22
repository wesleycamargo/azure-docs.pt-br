---
title: "Matriz de suporte do Azure Site Recovery para replicação de VMs da VMware e servidores físicos para o Azure | Microsoft Docs"
description: "Resume os sistemas operacionais e componentes compatíveis para replicar VMs da VMware no Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 837d53c4a70353c92de2475bb355051fdb2fcbb2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matriz de suporte para replicação de VMware e servidor físico no Azure


Este artigo resume os componentes compatíveis e as configurações de recuperação de desastre de VMs da VMware no Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Detalhes** 
--- | --- 
**VMs VMware** | Execute a recuperação de desastre de VMs da VMware locais no Azure. É possível implantar este cenário no portal do Azure ou usando o PowerShell.
**Servidores físicos** | Execute a recuperação de desastre de servidores físicos do Windows/Linux locais no Azure. Implante esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
**VMware** | vCenter Server 6.5, 6.0 ou 5.5 ou vSphere 6.5, 6.0, 5.5 | Recomendamos o uso de um vCenter Server
**Servidores físicos** | ND


## <a name="replicated-machines"></a>Computadores replicados

A tabela a seguir resume o suporte de replicação para computadores. O Site Recovery é compatível com a replicação de qualquer carga de trabalho em execução em um computador com um sistema operacional compatível.

**Componente** | **Detalhes**
--- | ---
Configuração do computador | Os computadores que são replicados para o Azure precisam atender aos [requisitos do Azure](#failed-over-azure-vm-requirements).
Sistema operacional do computador (Windows) | Windows Server 2016 de 64 bits (Server Core, Server com Desktop Experience)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1
Sistema operacional do computador (Linux) | Red Hat Enterprise Linux: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4 <br/><br/>CentOS: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4 <br/><br/>Servidor do Ubuntu 14.04 LTS[ (suporte para versões de kernel)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Servidor LTS do Ubuntu 16.04[ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Não há suporte para a atualização de computadores de replicação de SLES 11 SP3 até SLES 11 SP4. Se um computador replicado tiver sido atualizado do SLES 11 SP3 para o SLES 11 SP4, você precisará desabilitar a replicação e proteger o computador novamente após a atualização).

>[!NOTE]
>
> - Em distribuições Linux, há suporte somente para os kernels de estoque que fazem parte da versão/atualização da versão secundária da distribuição.
>
> - Atualizações de versões principais de uma distribuição Linux em uma máquina virtual do VMware protegida pelo Azure Site Recovery ou em um servidor físico não são compatíveis. Durante a atualização do sistema operacional em versões principais (por exemplo, CentOS 6.* para CentOS 7.\*), desabilite a replicação para a máquina, atualize o sistema operacional nela, em seguida, habilite a replicação novamente.
>

### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu


**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic a 3.13.0-132-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic a 3.13.0-137-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic a 4.4.0-96-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic a 4.4.0-104-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Configurações de armazenamento dos sistemas de arquivos/convidado do Linux

**Componente** | **Com suporte**
--- | ---
Sistemas de arquivos | ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
Gerenciador de volumes | LVM2
Software de múltiplos caminhos | Mapeador de Dispositivos
Dispositivos de armazenamento paravirtualizados | Não há suporte para dispositivos exportados por drivers paravirtualizados.
Dispositivos de E/S de bloqueio de várias filas | Sem suporte.
Servidores físicos com o controlador de armazenamento CCISS da HP | Sem suporte.
Diretórios | Esses diretórios (se configurados como partições/sistemas de arquivos separados) precisam estar no mesmo disco do sistema operacional no servidor de origem: /(raiz), /boot, /usr, /usr/local, /var, /etc </br></br>  Se o volume / (raiz) é um volume LVM, /boot precisa residir em uma partição separada no mesmo disco e não ser um volume LVM.<br/><br/>
XFSv5 | Os recursos de XFSv5 são compatíveis com sistemas de arquivos XFS, como soma de verificação de metadados, da versão 9.10 em diante do Serviço de mobilidade. Use o utilitário xfs_info para verificar o superbloco XFS da partição. Se ftype é definido como 1, isso indica que os recursos do XFSv5 estão sendo usados.



## <a name="network"></a>Rede

**Componente** | **Com suporte** 
--- | --- 
Agrupamento NIC da rede do host | Compatível com VMs da VMware <br/><br/>Sem suporte para a replicação de computador físico
VLAN da rede do host | sim 
IPv4 da rede do host | sim 
IPv6 da rede do host | Não  
Agrupamento NIC da rede do convidado/servidor | Não  
IPv4 da rede do convidado/servidor | sim 
IPv6 da rede do convidado/servidor | Não  
IP estático da rede do convidado/servidor (Windows) | sim 
IP estático da rede do convidado/servidor (Linux) | sim <br/><br/>As VMs são configuradas para usar o DHCP no failback  
Várias NICs da rede do convidado/servidor | sim 


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após o failover)

**Componente** | **Com suporte** 
--- | --- 
ExpressRoute | sim 
ILB | sim 
ELB | sim 
Gerenciador de Tráfego | sim 
NIC múltipla | sim 
Endereço IP Reservado | sim 
IPv4 | sim 
Manter endereço IP de origem | sim 
Pontos de extremidade de serviço de rede virtual<br/><br/> (Firewalls de armazenamento do Azure e VNETs) | Não  


## <a name="storage"></a>Armazenamento


**Componente** | **Com suporte** 
--- | --- 
NFS do host | Sim para VMware<br/><br/> Não para servidores físicos 
SAN (ISCSI) do host | sim
MPIO (Múltiplos caminhos) do host | Sim – testado com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
VMDK do convidado/servidor | sim 
EFI/UEFI do convidado/servidor| Parcial (migração para o Azure somente para Windows Server 2012 e posterior). </br></br> ** Consulte a observação ao final da tabela.
Disco de cluster compartilhado do convidado/servidor | Não  
Disco criptografado do convidado/servidor | Não  
NFS do convidado/servidor | Não  
SMB 3.0 do convidado/servidor | Não 
RDM do convidado/servidor | sim<br/><br/> N/D para servidores físicos 
Disco do convidado/servidor > 1 TB | sim<br/><br/>Até 4.095 GB 
Disco do convidado/servidor com tamanho de setor lógico e físico de 4.000 cada | sim
Disco do convidado/servidor com tamanho de setor lógico de 4.000 e físico de 512 bytes | sim 
Volume do convidado/servidor com discos distribuídos > 4 TB <br><br/>Gerenciamento de Volume lógico LVM | sim
Convidado/servidor - espaços de armazenamento | Não  
Adicionar/remover disco a quente por convidado/servidor | Não  
Convidado/servidor - excluir disco | sim 
Múltiplos caminhos (MPIO) de convidado/servidor | N/D

> [!NOTE]
> Máquinas virtuais da VMware com inicialização ** UEFI ** ou Servidores físicos com Windows Server 2012 ou posterior podem ser migrados para o Azure. As restrições a seguir se aplicam.
> - Há suporte apenas para a migração para o Azure. O failback não é compatível com o site do VMware local.
> - O servidor não deve ter mais de 4 partições no disco do sistema operacional.
> - Exige o Serviço de mobilidade do Azure Site Recovery versão 9.13 ou posterior.


## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Com suporte** 
--- | --- 
LRS | sim 
GRS | sim 
RA-GRS | sim 
Armazenamento frio | Não  
Armazenamento quente| Não  
Blobs de blocos | Não  
Criptografia em repouso (SSE)| sim 
Armazenamento Premium | sim 
Serviço de importação/exportação | Não  
Pontos de extremidade de serviço de rede virtual<br/><br/> Firewalls do armazenamento do Azure e VNETs configuradas na conta de armazenamento de cache/armazenamento de destino (usada para armazenar dados de replicação) | Não  
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Não  


## <a name="azure-compute"></a>Computação do Azure

**Recurso** | **Com suporte** 
--- | --- 
Conjuntos de disponibilidade | sim 
HUB | sim   
Discos gerenciados | sim 

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

VMs locais que são replicados para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | Verificar os [sistemas operacionais compatíveis](#replicated machines) | A verificação de pré-requisitos falha quando não há suporte para ela.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falha quando não há suporte para ela
**Tamanho de disco do sistema operacional** | Até 2.048 GB | A verificação de pré-requisitos falha quando não há suporte para ela
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 64 ou menos se você estiver replicando **VMs do VMware no Azure**; 16 ou menos se você estiver replicando **VMs do Hyper-V no Azure** | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 4.095 GB | A verificação de pré-requisitos falha quando não há suporte para ela
**Adaptadores de rede** | Há suporte para vários adaptadores | 
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falha quando não há suporte para ela
**Disco FC** | Sem suporte | A verificação de pré-requisitos falha quando não há suporte para ela
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O Bitlocker precisa ser desabilitado antes de habilitar a replicação em um computador.
**Nome da VM** | 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades do computador no Site Recovery.
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | VMs da Geração 2 com um tipo de disco básico de SO (que inclui um ou dois volumes de dados formatados como VHDX) e suporte para menos de 300 GB de espaço em disco.<br></br>Não há suporte para VMs Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="vault-tasks"></a>Tarefas do Vault

**Ação** | **Com suporte** 
--- | --- 
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  


## <a name="mobility-service"></a>Serviço de mobilidade

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
** Configuração Unificada do Azure Site Recovery ** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Próximas etapas
[Saiba como](tutorial-prepare-azure.md) para preparar o Azure para recuperação de desastres de máquinas virtuais da VMware.
