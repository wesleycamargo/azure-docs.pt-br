---
title: Matriz de suporte do Azure Site Recovery para recuperação de desastre de VMs IaaS do Azure entre regiões do Azure com o Azure Site Recovery | Microsoft Docs
description: Resume os sistemas operacionais com suporte e as configurações de replicação do Azure Site Recovery de VMs (máquinas virtuais) do Azure de uma região para outra, de acordo com as necessidades de DR (recuperação de desastre).
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: f8f529ecc21e8d9ecf149edb8bdf45e8b20dc283
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241249"
---
# <a name="support-matrix-for-disaster-recovery-of-azure-vms-between-azure-regions"></a>Matriz de suporte para recuperação de desastre de VMs do Azure entre regiões do Azure

Este artigo resume as configurações e os componentes com suporte quando você implanta a recuperação de desastres com replicação, failover e recuperação de VMs do Azure de uma região do Azure para outra, usando o serviço do [Azure Site Recovery](site-recovery-overview.md).

## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Método de implantação** |  **Com suporte/Sem suporte**
--- | ---
**Portal do Azure** | Com suporte
**PowerShell** | [Replicação do Azure para o Azure com o PowerShell](azure-to-azure-powershell.md)
**API REST** | Sem suporte no momento
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte de recurso

**Ação de recurso** | **Detalhes**
--- | --- | --- 
**Mover cofre entre grupos de recursos** | Sem suporte
**Mover recursos de computação/armazenamento/rede entre os grupos de recursos** | Sem suporte.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento / rede, depois que a VM estiver replicando, será necessário desabilitar e reativar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastres** | Com suporte no mesmo locatário do Azure Active Directory. 
**Migre as VMs pelas regiões dos clusters geográficos suportados (dentro e entre assinaturas)** | Com suporte no mesmo locatário do Azure Active Directory.
**Migrar máquinas virtuais na mesma região** | Sem suporte.

# <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico.

**Cluster geográfico** | **Regiões do Azure**
-- | --
América | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Oeste do Reino Unido, Sul do Reino Unido, Norte da Europa, Europa Ocidental, França Central, Sul da França
Ásia | Sul da Índia, Índia Central, Sudeste Asiático, Ásia Oriental, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia
Austrália   | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, US DoD Leste, US DoD Central
Alemanha | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China

>[!NOTE]
>
> Para a região Sul do Brasil, é possível apenas replicar e fazer failover para uma das regiões Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2 e Centro-Norte dos EUA e fazer failback.

## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento em cache usada pelo Site Recovery durante a replicação.

**Configuração** | **Detalhes**
--- | ---
Contas de armazenamento do uso geral V2 (quente e a camada fria) | Sem suporte. | A limitação existe para o armazenamento em cache porque os custos de transação para V2 são substancialmente maiores que as contas de armazenamento V1.
Firewalls de Armazenamento do Azure para redes virtuais  | Não  | Permitir o acesso a redes virtuais específicas do Azure em contas de armazenamento em cache usadas para armazenar dados replicados não é suportado.



## <a name="replicated-machine-operating-systems"></a>Sistemas Operacionais Replicados de Máquinas

O Site Recovery oferece suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção.

### <a name="windows"></a>Windows

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2016  | Server Core, Server com experiência Desktop
Windows Server 2012 R2 | 
Windows Server 2012 | 
Windows Server 2008 R2 | Executando o SP1 ou posterior

#### <a name="linux"></a>Linux

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
Ubuntu 14.04 LTS Server | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servidores Ubuntu usando autenticação e login baseados em senha, e o pacote cloud-init para configurar VMs em nuvem, podem ter o login baseado em senha desabilitado no failover (dependendo da configuração do cloudinit). O logon baseado em senha pode ser reativado na máquina virtual redefinindo a senha no menu Suporte> Solução de problemas> Configurações (da VM com falha no portal do Azure.
Debian 7 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel com suporte)](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3. [Versões de kernel com suporte](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não há suporte para atualização de replicação de máquinas de SP3 para SP4. Se uma máquina replicada tiver sido atualizada, será necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7<br/><br/> Rodando o kernel compatível com Red Hat ou o Unbreakable Enterprise Kernel Release 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-generic a 3.13.0-153-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic até 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic a 3.13.0-151-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic a 3.13.0-147-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic para 3.13.0-144-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-119-generic, |
|||
16.04 LTS | 9.19 | 4.4.0-21-generic até 4.4.0-131-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic até 4.15.0-30-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure até 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-generic a 4.4.0-128-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-generic a 4.4.0-124-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-41-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-generic para 4.4.0-119-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-38-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1012-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 para 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 até 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 até 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 a 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 para 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.5-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.19 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default até 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.18 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default a 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.17 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default a 4.4.126-94.22-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de arquivos Linux / armazenamento convidado 

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
* Gerenciador de volumes: LVM2
* Software Multipath: Mapeador de dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de computação

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Com suporte | Se você habilitar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente com base nas configurações da região de origem. Você pode modificar essas configurações. 
Zonas de disponibilidade | Sem suporte | Atualmente, você não pode replicar VMs implantadas em zonas de disponibilidade.
Benefício de uso híbrido (HUB) | Com suporte | Se a VM de origem tiver uma licença do HUB ativada, um failover de teste ou falha na VM também usará a licença do HUB.
Conjuntos de dimensionamento da VM | Sem suporte |
Imagens da galeria do Azure - Microsoft publicada | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da Galeria do Azure – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
VMs migradas com o Site Recovery | Com suporte | Se uma VM VMware ou uma máquina física foi migrada para o Azure usando o Site Recovery, você precisará desinstalar a versão mais antiga do serviço Mobility em execução na máquina e reiniciá-la antes de replicá-la para outra região do Azure.

## <a name="replicated-machines---disk-actions"></a>As máquinas - ações de disco replicadas

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco na VM replicada | Com suporte
Adicionar um disco a uma VM replicada | Sem suporte.<br/><br/> Você precisa desabilitar a replicação para a VM, adicione o disco e, em seguida, habilite a replicação novamente.

## <a name="replicated-machines---storage"></a>Máquinas replicadas - armazenamento

Esta tabela resumiu o suporte ao disco do SO do Azure VM, ao disco de dados e ao disco temporário.

- É importante observar os limites de disco e os destinos da VM para [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [VMs do Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar problemas de desempenho.
- Se você implantar com as configurações padrão, o Site Recovery criará automaticamente discos e contas de armazenamento com base nas configurações de origem. 
- Se você personalizar, certifique-se de seguir as diretrizes. 

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco do sistema operacional | 2048 GB | [Saiba mais](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms) sobre discos de VM.
Disco temporário | Sem suporte | O disco temporário é sempre excluído da replicação.<br/><br/> Não faça nenhum dado persistente no disco temporário. [Saiba mais](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Tamanho máximo do disco de dados | 4095 GB | 
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior que a máxima, a replicação não será recuperada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá recuperar, mas você poderá ver pontos de recuperação um pouco atrasados.
Disco de dados - conta de armazenamento padrão | Com suporte |
Disco de dados - conta de armazenamento premium | Com suporte | Se uma VM tiver discos distribuídos em contas de armazenamento premium e padrão, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado - standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |  
Disco gerenciado - premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Redundância | Há suporte para LRS e GRS.<br/><br/> Não há suporte para ZRS.
Armazenamento frio e quente | Sem suporte | Discos de VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Com suporte |         
Criptografia em repouso (SSE) | Com suporte | SSE é a configuração padrão em contas de armazenamento.   
Criptografia de Disco do Azure (ADE) para Windows | As VMs ativadas para [criptografia com o aplicativo do Azure AD](https://aka.ms/ade-aad-app) são suportadas. |
Azure Disk Encryption (ADE) para Linux | Sem suporte |
Adição quente / remoção de disco    | Sem suporte | Se você adicionar ou remover o disco de dados na VM, precisará desativar a replicação para a VM e reativá-la.
Exclusão de disco | Sem suporte | O disco temporário é excluído por padrão.
Espaços de armazenamento Diretos  | Sem suporte 
Servidor de Arquivos de Expansão  | Sem suporte 




## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi ativada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará o número de NICs na VM replicada após o failover.
Balanceador de Carga de Internet | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Com suporte | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG em NIC | Com suporte | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.  
NSG na sub-rede | Com suporte | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Com suporte | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ela será atribuída à VM com falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede na **itens replicados** > **configurações** > **de computação e rede**  >  **Interfaces de rede**. 
Endereço IP dinâmico | Com suporte | Se a NIC de origem tiver o endereçamento IP dinâmico, a NIC na VM com failover também é dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário. 
Gerenciador de Tráfego     | Com suporte | Você pode pré-configurar o Traffic Manager para que o tráfego seja roteado para o terminal na região de origem regularmente e para o terminal na região de destino em caso de failover.
DNS do Azure | Com suporte |
DNS Personalizado  | Com suporte |    
Proxy não autenticado | Com suporte | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md).   
Proxy autenticado | Sem suporte | Você não pode replicar uma VM que está usando um proxy autenticado para conectividade de saída     
VPN site a site | Suporte para com ou sem o ExpressRoute | Assegure-se de que os UDRs e os NSGs estejam configurados de modo que o tráfego do Site Recovery não seja roteado para o local. [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md).
Conexão de rede virtual para rede virtual | Com suporte |[Saiba mais](site-recovery-azure-to-azure-networking-guidance.md). 
Pontos de extremidade de serviço de rede virtual | Com suporte | Observe que os firewalls de armazenamento do Azure para redes virtuais não são suportados. Além disso, não é possível permitir o acesso a redes virtuais específicas do Azure em contas de armazenamento em cache usadas para armazenar dados replicados.
Redes aceleradas | Com suporte | A rede acelerada deve estar ativada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).


## <a name="next-steps"></a>Próximas etapas
- Leia [orientação de rede para replicar as VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Implante a recuperação de desastres [replicando as VMs do Azure](site-recovery-azure-to-azure.md).
