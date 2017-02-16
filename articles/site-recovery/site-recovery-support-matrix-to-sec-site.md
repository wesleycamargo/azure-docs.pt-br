---
title: "Matriz de suporte do Azure Site Recovery para replicar para o site secundário | Microsoft Docs"
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>Matriz de suporte do Azure Site Recovery de replicação para o site secundário proprietários do cliente

> [!div class="op_single_selector"]
> * [Replicar para o Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicar para o site secundário de propriedade do cliente](site-recovery-support-matrix-to-sec-site.md)

Uma lista de pré-requisitos do Azure Site Recovery é mencionada [aqui](site-recovery-best-practices.md) e o artigo a seguir resume componentes e configurações com suporte para o Azure Site Recovery ao replicar e recuperar para o site secundário de propriedade do cliente.

## <a name="support-for-deployment-options"></a>Suporte para opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (sem VMM)** | **Hyper-V (com o VMM)**
--- | --- | --- | ---
**Portal do Azure** | VMs do VMware locais para o site secundário do VMware.<br/><br/> Baixe o guia de ajuda](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o Guia de ajuda do InMage Scout. Não disponível no Portal do Azure | Sem suporte | VMs do Hyper-V locais em nuvens de VMM para uma nuvem de VMM secundária<br/><br/> Somente Replicação Hyper-V Standard, sem suporte a SAN
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Sem suporte | Somente modo de manutenção.
**PowerShell** | Sem suporte. | Sem suporte | Suportado



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
**Hyper-V (sem VMM)** | Não é uma configuração com suporte de replicação para o site secundário
**Hyper-V com o VMM** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes<br/><br/> Os hosts do Windows Server 2016 devem ser gerenciados pelo SCVMM 2016

## <a name="support-for-replicated-machine-machine-os-versions"></a>Suporte para versões de sistema operacional de computador replicado
A tabela abaixo resume o suporte do sistema operacional em vários cenários de implantação durante o uso do Azure Site Recovery. Esse suporte é **aplicável para qualquer carga de trabalho** em execução no sistema operacional mencionado.

**Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 e 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualquer SO convidado [com suporte do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Suporte de armazenamento para versões do Linux** Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS) Mapeador de Dispositivo por software multipath (multipath) Gerenciador de volumes: LVM2 **Não** há suporte para servidores físicos com o armazenamento do controlador HP CCISS.
>Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Suporte para rede
As tabelas abaixo resumem o suporte à configuração de rede em vários cenários de implantação usando o Azure Site Recovery para replicar para o Azure.

### <a name="host-network-configuration"></a>Configuração de rede do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Sim | Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

### <a name="guest-vm-network-configuration"></a>Configuração de rede da VM convidada

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim | Sim
NIC múltipla | Sim | Sim


## <a name="support-for-storage"></a>Suporte para armazenamento
As tabelas abaixo resumem o suporte de configuração de armazenamento em vários cenários de implantação usando o Azure Site Recovery para replicar para o Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do host

**Armazenamento (host)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
NFS | Sim | ND
SMB 3.0 | ND | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (MPIO) | Sim | Sim

### <a name="guest-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico/convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
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

**Ação** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não

## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o Agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver nenhum servidor VMM | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | NA (disponível no portal) | ND


## <a name="next-steps"></a>Próximas etapas
[Preparar para a implantação](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


