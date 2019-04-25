---
title: Matriz de suporte para recuperação de desastre de VMs do Azure entre regiões do Azure com o Azure Site Recovery | Microsoft Docs
description: Resume os sistemas operacionais com suporte e as configurações de replicação do Azure Site Recovery de VMs (máquinas virtuais) do Azure de uma região para outra, de acordo com as necessidades de DR (recuperação de desastre).
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/22/2019
ms.author: raynew
ms.openlocfilehash: c64148fbc0432bd25c5b02fb20b3e44134c1d9d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502073"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matriz de suporte para replicar VMs do Azure de uma região para outra

Este artigo resume as configurações e os componentes com suporte quando você implanta a recuperação de desastres com replicação, failover e recuperação de VMs do Azure de uma região do Azure para outra, usando o serviço do [Azure Site Recovery](site-recovery-overview.md).


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implantação** |  **Suporte**
--- | ---
**Portal do Azure** |  Com suporte.
**PowerShell** |  Com suporte. [Saiba mais](azure-to-azure-powershell.md)
**API REST** |  Com suporte.
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte de recurso

**Ação de recurso** | **Detalhes**
--- | --- | ---
**Mover cofres entre grupos de recursos** | Sem suporte
**Mover recursos de computação/armazenamento/rede entre os grupos de recursos** | Sem suporte.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento / rede, depois que a VM estiver replicando, será necessário desabilitar e reativar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastres** | Com suporte no mesmo locatário do Azure Active Directory.
**Migre as VMs pelas regiões dos clusters geográficos suportados (dentro e entre assinaturas)** | Com suporte no mesmo locatário do Azure Active Directory.
**Migrar máquinas virtuais na mesma região** | Sem suporte.

## <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico. Clusters geográficos são definidos mantendo a latência de dados e a soberania em mente.


**Cluster geográfico** | **Regiões do Azure**
-- | --
América | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Oeste do Reino Unido, Sul do Reino Unido, Norte da Europa, Europa Ocidental, França Central, Sul da França
Ásia | Sul da Índia, Índia Central, Sudeste Asiático, Ásia Oriental, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia
Austrália   | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, US DoD Leste, US DoD Central
Alemanha | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China, Norte da China2, Leste da China2

>[!NOTE]
>
> - Para **Sul do Brasil**, você pode replicar e fazer failover para essas regiões: Centro-Sul dos EUA, oeste dos EUA, Leste dos EUA, Leste dos EUA 2, oeste dos EUA, oeste dos EUA 2 e Centro-Norte dos EUA.
> - Sul do Brasil só pode ser usado como uma região de origem do qual as VMs podem replicar usando o Site Recovery. Ele não pode atuar como uma região de destino. Isso é devido a problemas de latência devido a distâncias geográficas.
> - Você pode trabalhar dentro de regiões em que você tenha acesso apropriado.
> - Se não mostrar a região na qual você deseja criar um cofre, verifique se que sua assinatura tem acesso para criar recursos nessa região.
> - Se você não conseguir ver uma região dentro de um cluster geográfico quando você habilita a replicação, verifique se que sua assinatura tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento em cache usada pelo Site Recovery durante a replicação.

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento do uso geral V2 (quente e a camada fria) | Sem suporte. | A limitação existe para o armazenamento em cache porque os custos de transação para V2 são substancialmente maiores que as contas de armazenamento V1.
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Caso esteja usando a conta de armazenamento de destino ou a conta de armazenamento de cache habilitada para firewall, escolha ['Permitir serviços confiáveis da Microsoft'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Sistemas Operacionais Replicados de Máquinas

O Site Recovery oferece suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção.

### <a name="windows"></a> Windows

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 |
Windows Server 2016  | Server Core, Server com experiência Desktop
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Executando o SP1 ou posterior

#### <a name="linux"></a>Linux

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servidores Ubuntu usando autenticação e login baseados em senha, e o pacote cloud-init para configurar VMs em nuvem, podem ter o login baseado em senha desabilitado no failover (dependendo da configuração do cloudinit). O logon baseado em senha pode ser reativado na máquina virtual redefinindo a senha no menu Suporte> Solução de problemas> Configurações (da VM com falha no portal do Azure.
Debian 7 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1,SP2,SP3,SP4. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não há suporte para atualização de replicação de máquinas de SP3 para SP4. Se uma máquina replicada tiver sido atualizada, será necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Rodando o kernel compatível com Red Hat ou o Unbreakable Enterprise Kernel Release 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.24 | 3.13.0-24-Generic para 3.13.0-167-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-143-generic,<br/>4.15.0-1023-Azure para 4.15.0-1040-azure |
14.04 LTS | 9.23 | 3.13.0-24-Generic para 3.13.0-165-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-142-generic,<br/>4.15.0-1023-Azure para 4.15.0-1037-azure |
14.04 LTS | 9.22 | 3.13.0-24-generic a 3.13.0-164-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-140-generic,<br/>4.15.0-1023-azure a 4.15.0-1036-azure |
14.04 LTS | 9.21 | 3.13.0-24-generic para 3.13.0-163-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-140-generic,<br/>4.15.0-1023-azure para 4.15.0-1035-azure |
|||
16.04 LTS | 9.24 | 4.4.0-21 para 4.4.0-143-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-46-generic<br/>4.11.0-1009-Azure para 4.11.0-1018-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1040-azure|
16.04 LTS | 9.23 | 4.4.0-21 para 4.4.0-142-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-45-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1037-azure|
16.04 LTS | 9.22 | 4.4.0-21-generic para 4.4.0-140-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-43-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1036-azure|
16.04 LTS | 9.21 | 4.4.0-21-generic para 4.4.0-140-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic para 4.15.0-42-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1035-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.21,9.22,9.23,9.24 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.21, 9.22, 9.23, 9.24 | 3.16.0-4-amd64 a 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.24 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) para 4.4.121-92.101-default</br></br>4.4.73-5-default SP3 para 4.4.175-94.79-default</br></br>4.12.14-94.41-default SP4 para 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.23 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) para 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default a 4.4.162-94.69-default</br></br>4.12.14-94.41-default SP4 para 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.22 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default a 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.21 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default a 4.4.162-94.72-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de arquivos Linux / armazenamento convidado

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS, BTRFS
* Gerenciador de volume: LVM2
* Software de múltiplos caminhos: Mapeador de Dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de computação

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Com suporte | Se você habilitar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente com base nas configurações da região de origem. Você pode modificar essas configurações.
Zonas de disponibilidade | Com suporte |
Benefício de uso híbrido (HUB) | Com suporte | Se a VM de origem tiver uma licença do HUB ativada, um failover de teste ou falha na VM também usará a licença do HUB.
Conjuntos de dimensionamento da VM | Sem suporte |
Imagens da galeria do Azure - Microsoft publicada | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da Galeria do Azure – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
VMs migradas com o Site Recovery | Com suporte | Se uma VM VMware ou uma máquina física foi migrada para o Azure usando o Site Recovery, você precisará desinstalar a versão mais antiga do serviço Mobility em execução na máquina e reiniciá-la antes de replicá-la para outra região do Azure.
Políticas de RBAC | Sem suporte | Políticas RBAC (controle) em VMs não são replicadas para o failover de VM na região de destino de acesso baseado em função.

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
Tamanho máximo do disco do sistema operacional | 2048 GB | [Saiba mais](../virtual-machines/windows/managed-disks-overview.md) sobre discos de VM.
Disco temporário | Sem suporte | O disco temporário é sempre excluído da replicação.<br/><br/> Não armazene nenhum dado persistente no disco temporário. [Saiba mais](../virtual-machines/windows/managed-disks-overview.md).
Tamanho máximo do disco de dados | 4095 GB |
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior que a máxima, a replicação não será recuperada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá recuperar, mas você poderá ver pontos de recuperação um pouco atrasados.
Disco de dados - conta de armazenamento padrão | Com suporte |
Disco de dados - conta de armazenamento premium | Com suporte | Se uma VM tiver discos distribuídos em contas de armazenamento premium e padrão, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado - standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Disco gerenciado - premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
SSD Standard | Com suporte |
Redundância | Há suporte para LRS e GRS.<br/><br/> Não há suporte para ZRS.
Armazenamento frio e quente | Sem suporte | Discos de VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Com suporte |
Criptografia em repouso (SSE) | Com suporte | SSE é a configuração padrão em contas de armazenamento.   
Habilitar o ADE (Azure Disk Encryption) para o sistema operacional Windows | As VMs habilitadas para [criptografia com o aplicativo do Azure AD](https://aka.ms/ade-aad-app) são compatíveis |
ADE (Azure Disk Encryption) para sistema operacional Linux | Sem suporte |
Adição/remoção de disco a quente | Sem suporte | Se você adicionar ou remover um disco de dados da VM, precisará desabilitar a replicação e habilitá-la novamente na VM.
Exclusão de disco | suporte. Você deve usar [Powershell](azure-to-azure-exclude-disks.md) para configurar. |  Por padrão, os discos temporários são excluídos.
Espaços de armazenamento Diretos  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
Servidor de Arquivos de Expansão  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
LRS | Com suporte |
GRS | Com suporte |
RA-GRS | Com suporte |
ZRS | Sem suporte |
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Se restringir o acesso à rede virtual para contas de armazenamento, habilite [permitir que os serviços Microsoft confiáveis](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Não  | Os custos das transações aumentam substancialmente em comparação com as contas de armazenamento V1 de uso geral

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de que você siga as metas de escalabilidade e desempenho de disco VM para [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) VMs. Se você usar as configurações padrão, o Site Recovery cria os discos necessários e a contas de armazenamento, com base na configuração de origem. Se você personaliza e selecionar suas próprias configurações, siga as metas de escalabilidade e desempenho de disco para VMs de origem.

## <a name="limits-and-data-change-rates"></a>Taxas de alteração de dados e limites

A tabela a seguir resume os limites do Site Recovery.

- Esses limites são baseados em nossos testes, mas, obviamente, não abrangem todas as combinações possíveis de aplicativos e/s.
- Os resultados reais podem variar dependendo da combinação do aplicativo e/s.
- Há dois limites a considerar, por disco de dados de variação e por dados da máquina virtual de variação.
- Por exemplo, se usarmos um disco Premium P20, conforme descrito na tabela a seguir, Site Recovery pode manipular a 5 MB de variação por disco, no máximo de cinco esses discos por VM, devido ao limite de variação total de 25 MB/s por VM.

**Destino de armazenamento** | **E/s de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi ativada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará o número de NICs na VM replicada após o failover. Também Observe que a ordem das NICs após o failover não é garantia de ser o mesmo que a ordem original.
Balanceador de Carga de Internet | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Com suporte | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG em NIC | Com suporte | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na sub-rede | Com suporte | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Com suporte | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ela será atribuída à VM com falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede na **itens replicados** > **configurações** > **de computação e rede**  >  **Interfaces de rede**.
Endereço IP dinâmico | Com suporte | Se a NIC de origem tiver o endereçamento IP dinâmico, a NIC na VM com failover também é dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário.
Vários endereços IP | Sem suporte | Quando você faz o failover de uma VM que tem uma NIC com vários endereços IP, somente o endereço IP primário do adaptador de rede na região de origem é mantido. Para atribuir vários endereços IP, você pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexará um script para atribuir endereços IP adicionais ao plano, ou você pode fazer a alteração manualmente ou com um script após o failover. 
Gerenciador de Tráfego     | Com suporte | Você pode pré-configurar o Traffic Manager para que o tráfego seja roteado para o terminal na região de origem regularmente e para o terminal na região de destino em caso de failover.
DNS do Azure | Com suporte |
DNS Personalizado  | Com suporte |
Proxy não autenticado | Com suporte | [Saiba mais]. (site-recovery-azure-to-azure-networking-guidance.md)   
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.    
Conexão do VPN site a site no local<br/><br/>(com ou sem o ExpressRoute)| Com suporte | Verifique se as UDRs e os NSGs são configurados de tal forma que o tráfego do Site Recovery não seja encaminhado para o local. [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)    
Conexão VNET a VNET | Com suporte | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)  
Pontos de extremidade de serviço de rede virtual | Com suporte | Se você estiver restringindo o acesso à rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft confiáveis tenham a permissão para acessar a conta de armazenamento.
Redes aceleradas | Com suporte | A rede acelerada deve estar ativada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Próximas etapas
- Leia [diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](site-recovery-azure-to-azure.md).
