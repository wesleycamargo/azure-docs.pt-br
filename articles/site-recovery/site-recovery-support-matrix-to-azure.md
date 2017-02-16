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
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 3b9912ac45627f799ef3ca20eccf8d11f9ae52f0


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicação no Azure

> [!div class="op_single_selector"]
> * [Replicar para o Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicar para o site secundário de propriedade do cliente](site-recovery-support-matrix-to-sec-site.md)


Uma lista de pré-requisitos do Azure Site Recovery é mencionada [aqui](site-recovery-best-practices.md), e o artigo a seguir resume os componentes e configurações com suporte para o Azure Site Recovery durante a replicação e a recuperação no Azure.


## <a name="support-for-deployment-options"></a>Suporte para opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
**Portal do Azure** | VMs do VMware locais para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | VMs do Hyper-V locais (e não nuvens de VMM) para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | VMs do Hyper-V locais em nuvens de MM para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção. | Somente modo de manutenção.
**PowerShell** | Não há suporte no momento. | Suportado | Suportado


## <a name="support-for-datacenter-management-servers"></a>Suporte para servidores de Gerenciamento do Data Center

### <a name="virtualization-management-entities"></a>Entidades de gerenciamento de virtualização

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vSphere 6.0, 5.5 ou 5.1 com a atualização mais recente
**Hyper-V (com o VMM)** | SCVMM 2016 e SCVMM 2012 R2

  >[!Note]
  > No momento, não há suporte para uma nuvem SCVMM 2016 com uma combinação de hosts Windows Server 2016 e 2012 R2.

### <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5) 
**Hyper-V (sem VMM)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes
**Hyper-V com o VMM** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes<br/><br/> Os hosts do Windows Server 2016 devem ser gerenciados pelo SCVMM 2016

  >[!Note]
  >No momento, não há suporte para um site do Hyper-V que combine hosts que executam o Windows Server 2016 e 2012 R2.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

Máquinas virtuais protegidas deve atender aos [Requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) ao replicar para o Azure.
A tabela abaixo resume o suporte a sistemas operacionais replicados em vários cenários de implantação durante o uso do Azure Site Recovery. Esse suporte é **aplicável para qualquer carga de trabalho** em execução no sistema operacional mencionado.

 **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 e 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx) | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!Note]
>**Suporte de armazenamento para versões do Linux** Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS) Mapeador de Dispositivo por software multipath (multipath) Gerenciador de volumes: LVM2 **Não** há suporte para servidores físicos com o armazenamento do controlador HP CCISS.
>Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Suporte para rede
As tabelas abaixo resumem o suporte à configuração de rede em vários cenários de implantação usando o Azure Site Recovery para replicar para o Azure.

### <a name="host-network-configuration"></a>Configuração de rede do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Agrupamento NIC | Sim<br/><br/>Sem suporte em computadores físicos| Sim | Sim
VLAN | Sim | Sim | Sim
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não

### <a name="guest-vm-network-configuration"></a>Configuração de rede da VM convidada

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Agrupamento NIC | Não | Não | Não
IPv4 | Sim | Sim | Sim
IPv6 | Não | Não | Não
IP estático (Windows) | Sim | Sim | Sim
IP estático (Linux) | Não | Não | Não
NIC múltipla | Sim | Sim | Sim

### <a name="failed-over-azure-vm-network-configuration"></a>Configuração de rede da VM do Azure após failover

**Rede do Azure** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
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
As tabelas abaixo resumem o suporte de configuração de armazenamento em vários cenários de implantação usando o Azure Site Recovery para replicar para o Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | ND | ND
SMB 3.0 | ND | Sim | Sim
SAN (ISCSI) | Sim | Sim | Sim
Múltiplos caminhos (MPIO)<br></br>Testado com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sim para VMware<br/><br/> | Sim | Sim

### <a name="guest-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico/convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
VMDK | Sim | ND | ND
VHD/VHDX | ND | Sim | Sim
VM ger 2 | ND | Sim | Sim
EFI/UEFI| Não | Sim | Sim
Disco de cluster compartilhado | Sim para VMware<br/><br/> ND para servidores físicos | Não | Não
Disco criptografado | Não | Não | Não
NFS | Não | ND | ND
SMB 3.0 | Não | Não | Não
RDM | Sim<br/><br/> ND para servidores físicos | ND | ND
Disco > 1 TB | Não | Não | Não
Volume com discos distribuídos > 1 TB<br/><br/> Gerenciamento de Volume lógico LVM | Sim | Sim | Sim
Espaços de Armazenamento | Não | Sim | Sim
Adição/remoção de disco a quente | Não | Não | Não
Exclusão de disco | Sim | Sim | Sim
Múltiplos caminhos (MPIO) | ND | Sim | Sim

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
LRS | Sim | Sim | Sim
GRS | Sim | Sim | Sim
Armazenamento frio | Não | Não | Não
Armazenamento quente| Não | Não | Não
Criptografia em repouso (SSE)| Sim | Sim | Sim
Armazenamento Premium | Sim | Não | Não
Serviço de importação/exportação | Não | Não | Não


## <a name="support-for-azure-compute-configuration"></a>Suporte para configuração de computação do Azure

**Recurso de computação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Conjuntos de disponibilidade | Não | Não | Não
HUB | Sim | Sim | Sim

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de VM do Azure após failover

Você pode implantar o Site Recovery para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte no Azure. Isso inclui a maioria das versões do Windows e do Linux. As VMs locais que você deseja replicar devem estar em conformidade com os requisitos do Azure listados abaixo ao replicar no Azure.

**Entidade** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Adaptadores de rede** | Há suporte para vários adaptadores |
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | As VMs da Geração 2 com um tipo de disco básico de sistema operacional, que inclui um ou dois volumes de dados formatados como VHDX e há suporte para menos de 300 GB.<br></br>Não há suporte para VMs do Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Suporte para ações do cofre dos Serviços de Recuperação

**Ação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não


## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o Agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver nenhum servidor VMM | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.3.4246.1 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | NA (disponível no portal) | ND
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado nos servidores Hyper-V locais (com ou sem um servidor VMM) | Agente mais recente ([disponível no portal](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Próximas etapas
[Preparar para a implantação](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


