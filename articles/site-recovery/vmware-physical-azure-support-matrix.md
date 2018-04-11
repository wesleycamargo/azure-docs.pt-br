---
title: Matriz de suporte para replicação de VMs VMware e servidores físicos para Azure com Azure Site Recovery | Microsoft Docs
description: Resume os sistemas operacionais e componentes com suporte para replicação de VMs VMware e servidor físico para Azure usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/29/2018
ms.author: raynew
ms.openlocfilehash: 28ddecc45faa213d1fd536b5ad8690e151037505
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matriz de suporte para replicação de VMware e servidor físico no Azure

Este artigo resume os componentes compatíveis e as configurações de recuperação de desastre de VMs da VMware no Azure usando o [Azure Site Recovery](site-recovery-overview.md).

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Detalhes**
--- | ---
VMs VMware | Execute a recuperação de desastre de VMs da VMware locais no Azure. É possível implantar este cenário no portal do Azure ou usando o PowerShell.
Servidores físicos | Execute a recuperação de desastre de servidores físicos do Windows/Linux locais no Azure. Implante esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
VMware | vCenter Server 6.5, 6.0 ou 5.5 ou vSphere 6.5, 6.0 ou 5.5 | Recomendamos o uso de um vCenter Server.
Físico | N/D


## <a name="replicated-machines"></a>Computadores replicados

A tabela a seguir resume o suporte de replicação para VMs VMware e servidores físicos. O Site Recovery é compatível com a replicação de qualquer carga de trabalho em execução em um computador com um sistema operacional compatível.

**Componente** | **Detalhes**
--- | ---
Configurações do computador | Os computadores que são replicados para o Azure precisam atender aos [requisitos do Azure](#azure-vm-requirements).
Sistema operacional Windows | Windows Server 2016 de 64 bits (Server Core, Server com Desktop Experience), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1. Não há suporte para Windows 2016 Nano Server.
Sistema operacional Linux | Red Hat Enterprise Linux: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4 <br/><br/>CentOS: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.4 <br/><br/>Servidor do Ubuntu 14.04 LTS[ (suporte para versões de kernel)](#ubuntu-kernel-versions)<br/><br/>Servidor LTS do Ubuntu 16.04[ (versões de kernel com suporte)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (versões de kernel com suporte)](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4, 6.5 que executa Red Hat compatível com kernel ou o Unbreakable Enterprise Kernel Versão 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>Não há suporte para computadores replicados do SP3 para SP4. Para atualizar, desabilite a replicação e habilite novamente após a atualização.

>[!NOTE]
>
> - Nas distribuições Linux, apenas os kernels de estoque que fazem parte da versão/atualização de versão secundária de distribuição têm suporte.
>
> - Não há suporte para atualização de computadores protegidos nas versões principais de distribuição do Linux. Para atualizar, desabilite a replicação, atualize o sistema operacional e, em seguida, habilite a replicação novamente.
>

### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu


**Versão com suporte** | **Serviço de Mobilidade do Azure Site Recovery versão** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic a 3.13.0-132-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic a 3.13.0-137-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-generic para 3.13.0-142-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-116-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic a 4.4.0-96-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic a 4.4.0-104-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-generic para 4.4.0-116-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic para 4.13.0-36-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1011-azure |


### <a name="debian-kernel-versions"></a>Versões de Kernel do Debian


**Versão com suporte** | **Serviço de Mobilidade do Azure Site Recovery versão** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.14 | 3.2.0-4-amd64 para 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14 | 3.16.0-4-amd64 para 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Sistemas de arquivos Linux/armazenamento convidado

**Componente** | **Com suporte**
--- | ---
Sistemas de arquivos | ext3, ext4, XFS.
Gerenciador de volumes | LVM2.
Software de múltiplos caminhos | Mapeador de Dispositivos.
Dispositivos de armazenamento paravirtualizados | Não há suporte para dispositivos exportados por drivers paravirtualizados.
Dispositivos de E/S de bloqueio de várias filas | Sem suporte.
Servidores físicos com o controlador de armazenamento CCISS da HP | Sem suporte.
Diretórios | Esses diretórios (se configurados como partições/sistemas de arquivos separados) precisam estar todos no mesmo disco do sistema operacional no servidor de origem: /(raiz), /boot, /usr, /usr/local, /var, /etc.</br></br> /boot deve estar em uma partição de disco e não ser um volume LVM.<br/><br/>
Requisitos de espaço livre| 2 GB na /partição raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | Recursos XFSv5 em sistemas de arquivos XFS, como a soma de verificação dos metadados, têm suporte do Serviço de Mobilidade versão 9.10 em diante. Use o utilitário xfs_info para verificar o superbloco XFS da partição. Se ftype é definido como 1, isso indica que os recursos do XFSv5 estão sendo usados.



## <a name="network"></a>Rede

**Componente** | **Com suporte**
--- | ---
Agrupamento NIC da rede do host | Compatível com VMs da VMware. <br/><br/>Sem suporte para a replicação de computador físico.
VLAN da rede do host | Sim.
IPv4 da rede do host | Sim.
IPv6 da rede do host | Nº
Agrupamento NIC da rede do convidado/servidor | Nº
IPv4 da rede do convidado/servidor | Sim.
IPv6 da rede do convidado/servidor | Nº
IP estático da rede do convidado/servidor (Windows) | Sim.
IP estático da rede do convidado/servidor (Linux) | Sim. <br/><br/>As VMs são configuradas para usar o DHCP no failback.
Várias NICs da rede do convidado/servidor | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após o failover)

**Componente** | **Com suporte**
--- | ---
Azure ExpressRoute | sim
ILB | sim
ELB | sim
Gerenciador de Tráfego do Azure | sim
NIC múltipla | sim
Endereço IP Reservado | sim
IPv4 | sim
Manter endereço IP de origem | sim
Pontos de extremidade de serviço de Rede Virtual do Azure<br/><br/> (Redes virtuais e firewalls do Armazenamento do Microsoft Azure) | Não 

## <a name="storage"></a>Armazenamento
**Componente** | **Com suporte**
--- | ---
NFS do host | Sim para VMware<br/><br/> Não para servidores físicos
SAN (ISCSI) do host | sim
MPIO (Múltiplos caminhos) do host | Sim, testado com Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
VMDK do convidado/servidor | sim
EFI/UEFI do convidado/servidor| Parcial (somente migração do Azure para o Windows Server 2012 e máquinas virtuais VMware posteriores) </br></br> Consulte a observação ao final da tabela
Disco de cluster compartilhado do convidado/servidor | Não 
Disco criptografado do convidado/servidor | Não 
NFS do convidado/servidor | Não 
SMB 3.0 do convidado/servidor | Não 
RDM do convidado/servidor | sim<br/><br/> N/D para servidores físicos
Disco do convidado/servidor > 1 TB | sim<br/><br/>Até 4.095 GB
Disco do convidado/servidor com tamanho de setor lógico e físico de 4.000 cada | sim
Disco do convidado/servidor com tamanho de setor lógico de 4.000 e físico de 512 bytes | sim
Volume do convidado/servidor com discos distribuídos >4 TB <br><br/>Gerenciamento de volumes lógicos (LVM)| sim
Convidado/servidor - espaços de armazenamento | Não 
Adicionar/remover disco a quente por convidado/servidor | Não 
Convidado/servidor - excluir disco | sim
MPIO (Múltiplos caminhos) de convidado/servidor | N/D

> [!NOTE]
> Máquinas virtuais da VMware com inicialização UEFI com Windows Server 2012 ou posterior podem ser migrados para o Azure. As restrições a seguir se aplicam:

> - Há suporte apenas para a migração para o Azure. O failback não é compatível com o site do VMware local.
> - O servidor não deve ter mais de quatro partições no disco do sistema operacional.
> - Exige o Serviço de Mobilidade versão 9.13 ou posterior.
> - Sem suporte para servidores físicos.

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Com suporte**
--- | ---
Armazenamento com redundância local | sim
Armazenamento com redundância geográfica | sim
Armazenamento com redundância geográfica com acesso de leitura | sim
Armazenamento frio | Não 
Armazenamento quente| Não 
Blobs de bloco | Não 
Criptografia em repouso (Criptografia do Serviço de Armazenamento)| sim
Armazenamento Premium | sim
Serviço de importação/exportação | Não 
Pontos de extremidade de serviço de Rede Virtual<br/><br/> Firewalls de armazenamento e redes virtuais configurados na conta de armazenamento de cache/armazenamento de destino (usada para armazenar dados de replicação) | Não 
Contas de armazenamento v2 de uso geral (camadas hot e cool) | Não 

## <a name="azure-compute"></a>Computação do Azure

**Recurso** | **Com suporte**
--- | ---
Conjuntos de disponibilidade | sim
HUB | sim
Discos gerenciados | sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

VMs locais que são replicados para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando o Site Recovery executar uma verificação de pré-requisitos, ele irá falhar se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verificar os [sistemas operacionais compatíveis](#replicated machines). | A verificação falha se não tiver suporte. 
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte. 
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte. 
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.  
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.  
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte. 
Adaptadores de rede | Há suporte para vários adaptadores. | 
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte. 
Disco FC | Sem suporte. | A verificação falha se não tiver suporte. 
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador. | 
Nome da VM | De 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.


## <a name="vault-tasks"></a>Tarefas do Vault

**Ação** | **Com suporte**
--- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não 
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não 


## <a name="mobility-service"></a>Serviço de Mobilidade

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
Configuração Unificada do Azure Site Recovery | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)
Serviço de Mobilidade | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar | 9.12.4653.1 (disponível no portal) | [Recursos e correções mais recentes](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Próximas etapas
[Saiba como](tutorial-prepare-azure.md) para preparar o Azure para recuperação de desastres de máquinas virtuais da VMware.
