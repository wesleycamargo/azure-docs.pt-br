---
title: "Matriz de suporte para replicação de Hyper-V no Azure | Microsoft Docs"
description: "Resume os componentes compatíveis e os requisitos para replicação de Hyper-V no Azure com o Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: 58d54c1e0e6aa88878b45400b9211396f5d1b9d5
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matriz de suporte para replicação de Hyper-V no Azure


Este artigo resume os componentes compatíveis e as configurações de recuperação de desastres de VMs Hyper-V locais para o Azure usando o serviço do [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Detalhes**
--- | --- 
**Hyper-V com o VMM** | Você pode executar a recuperação de desastres para o Azure para VMs em execução em hosts Hyper-V gerenciados na malha do System Center Virtual Machine Manager (VMM)<br/><br/> É possível implantar este cenário no portal do Azure ou usando o PowerShell.<br/><br/> Quando hosts Hyper-V são gerenciadas pelo VMM, você também pode executar a recuperação de desastres em um site local secundário. Leia [este artigo](tutorial-vmm-to-vmm.md) para saber mais sobre este cenário.
**Hyper-V sem VMM** | Você pode executar a recuperação de desastres para o Azure para VMs em execução em hosts Hyper-V que não são gerenciados pelo VMM.<br/><br/> É possível implantar este cenário no portal do Azure ou usando o PowerShell. 


## <a name="on-premises-servers"></a>Servidores locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
**Hyper-V (em execução sem o VMM)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes. | Quando você configura um site do Hyper-V no Site Recovery, a mistura de hosts que executam o Windows Server 2016 e o 2012 R2 não tem suporte.<br/><br/> Para VMs localizadas em um host executando o Windows Server 2016, a recuperação para um local alternativo não tem suporte.
**Hyper-V (em execução com o VMM)** | VMM 2016, VMM 2012 R2 | Se for usado o VMM, os hosts do Windows Server 2016 deverão ser gerenciados no VMM 2016.<br/><br/> No momento, nuvens de VMM que misturam hosts Hyper-V executados no Windows Server 2016 e no 2012 R2 não são compatíveis.<br/><br/> Ambientes que incluem uma atualização de um servidor 2012 R2 existente do VMM para o 2016 não são compatíveis.


## <a name="replicated-vms"></a>VMs replicadas


A tabela a seguir resume o suporte de VMs. O Site Recovery é compatível com qualquer carga de trabalho em execução em um sistema operacional compatível. 

 **Componente** | **Detalhes**
--- | ---
Configuração da VM | VMs que são replicadas para o Azure devem atender aos [requisitos do Azure](#failed-over-azure-vm-requirements).
Sistema operacional convidado | Qualquer SO convidado [compatível com o Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> O Windows Server 2016 Nano Server não é compatível.




## <a name="hyper-v-network-configuration"></a>Configuração de rede Hyper-V

**Componente** | **Hyper-V com o VMM** | **Hyper-V sem VMM**
--- | --- | ---
Rede do host: Agrupamento NIC | sim
Rede do host: VLAN | sim
Rede do host: IPv4 | sim
Rede do host: IPv6 | Não 
Rede da VM convidada: Agrupamento NIC | Não 
Rede da VM convidada: IPv4 | sim
Rede da VM convidada: IPv6 | Não 
Rede da VM convidada: IP estático (Windows) | sim
Rede da VM convidada: IP estático (Linux) | Não 
Rede da VM convidada: Multi-NIC | sim



## <a name="azure-vm-network-configuration-after-failover"></a>Configuração de rede da VM do Azure (após failover)

**Componente** | **Hyper-V com o VMM** | **Hyper-V sem VMM**
--- | --- | ---
ExpressRoute | sim | sim
ILB | sim | sim
ELB | sim | sim
Gerenciador de Tráfego | sim | sim
NIC múltipla | sim | sim
IP Reservado | sim | sim
IPv4 | sim | sim
Manter endereço IP de origem | sim | sim
Pontos de extremidade de serviço de VNET<br/><br/> (Redes virtuais e firewalls do Armazenamento do Azure) | Não  | Não 


## <a name="hyper-v-host-storage"></a>Armazenamento de host do Hyper-V

**Armazenamento** | **Hyper-V com o VMM** | Hyper-V sem VMM
--- | --- | --- | ---
NFS | ND | ND
SMB 3.0 | sim | sim
SAN (ISCSI) | sim | sim
Múltiplos caminhos (MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM para CLARiiON | sim | sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento de convidado da VM do Hyper-V

**Armazenamento** | **Hyper-V com o VMM** | Hyper-V sem VMM
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | sim | sim
VM geração 2 | sim | sim
EFI/UEFI| sim | sim
Disco de cluster compartilhado | Não  | Não 
Disco criptografado | Não  | Não 
NFS | ND | ND
SMB 3.0 | Não  | Não 
RDM | ND | ND
Disco > 1 TB | Sim, até 4095 GB | Sim, até 4095 GB
Disco: setor de lógica e física de 4K | Não compatível: Ger 1/Ger 2 | Não compatível: Ger 1/Ger 2
Disco: setor de lógica e física de 4K e 512 bytes | sim |  sim
Volume com discos distribuídos > 1 TB<br/><br/> Gerenciamento de Volume lógico LVM | sim | sim
Espaços de Armazenamento | sim | sim
Adição/remoção de disco a quente | Não  | Não 
Exclusão de disco | sim | sim
Múltiplos caminhos (MPIO) | sim | sim

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Hyper-V com o VMM** | **Hyper-V sem VMM)**
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
Pontos de extremidade da VNET (VNETs e firewalls do armazenamento do Azure), na conta de armazenamento de cache de destino usada para dados de replicação | Não  | Não 


## <a name="azure-compute-features"></a>Recursos de computação do Azure

**Recurso** | **Hyper-V com o VMM** | **Hyper-V sem VMM**
--- | --- | ---
Conjuntos de disponibilidade | sim | sim
HUB | sim | sim  
Discos gerenciados | Sim, para failover<br/><br/> O failback de discos gerenciados não é compatível | Sim, para failover<br/><br/> O failback de discos gerenciados não é compatível

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

VMs locais que são replicados para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | O Site Recovery é compatível com todos os sistemas operacionais que têm [suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte.
**Tamanho de disco do sistema operacional** | Até 2048 GB para máquinas virtuais de Geração 1.<br/><br/> Até 300 GB para máquinas virtuais de Geração 2.  | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 16 ou menos  | A verificação de pré-requisitos falhará se não houver suporte.
**Tamanho do VHD do disco de dados** | Até 4.095 GB | A verificação de pré-requisitos falhará se não houver suporte.
**Adaptadores de rede** | Há suporte para vários adaptadores |
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte.
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte.
**Formato de disco rígido** | VHD  <br/><br/> VHDX | O Site Recovery converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O Bitlocker deve ser desabilitado antes de habilitar a replicação para uma VM.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. O nome da VM deve começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da VM no Site Recovery.
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | VMs da Geração 2 com um tipo de disco básico de SO (que inclui um ou dois volumes de dados formatados como VHDX) e suporte para menos de 300 GB de espaço em disco.<br></br>Não há suporte para VMs Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Ações de cofre dos Serviços de Recuperação

**Ação** |  **Hyper-V com o VMM** | **Hyper-V sem VMM**
--- | --- | --- 
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  | Não  
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não  | Não  


## <a name="provider-and-agent"></a>Provedor e agente

Para certificar-se de que a implantação seja compatível com as configurações neste artigo, verifique se você está executando as versões de agente e provedor mais recentes.

**Nome** | **Descrição** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Hyper-V com VMM: instalado nos servidores do VMM<br/><br/> Hyper-V sem VMM: instalado em hosts Hyper-V| Versão mais recente: 5.1.2700.1 (disponível no portal)<br/><br/> [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado nos servidores Hyper-V locais (com ou sem VMM) | Agente mais recente disponível no portal






## <a name="next-steps"></a>Próximas etapas
Saiba como [preparar o Azure](tutorial-prepare-azure.md) para a recuperação de desastre de VMs locais do Hyper-V. 
