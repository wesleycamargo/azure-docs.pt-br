---
title: Novidades do Azure Site Recovery | Microsoft Docs
description: Fornece um resumo dos novos recursos introduzidos no Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211110"
---
# <a name="whats-new-in-site-recovery"></a>Novidades do Site Recovery

O serviço [Azure Site Recovery](site-recovery-overview.md) é atualizado e aprimorado continuamente. Para ajudar você a se manter atualizado, este artigo fornece informações sobre as últimas versões, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Caso você tenha sugestões de recursos do Site Recovery, adoraríamos [ouvir seus comentários](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>T1 2019

### <a name="linux-support"></a>Suporte para Linux

O [pacote cumulativo de atualizações 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece uma atualização para os agentes e provedores do Site Recovery. As atualizações adicionam suporte para o Linux da seguinte maneira:

- **Recuperação de desastre de VMs do Azure**: Red Hat Workstation 6/7; novas versões do kernel para Ubuntu, Debian e SUSE.
- **Recuperação de desastre de servidores físicos/VMs da VMware para o Azure**: Red Hat Enterprise Linux 7.6; Red Hat Workstation 6/7; Oracle Linux 6.10/7.6; novas versões do kernel para Ubuntu, Debian e SUSE.



## <a name="q4-2018"></a>T4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de Preços para recuperação de desastre da VM do Azure

A recuperação de desastre para VMs do Azure gera custos de licenciamento da VM e custos de rede e armazenamento. O Azure fornece uma [Calculadora de Preços](https://aka.ms/a2a-cost-estimator) para ajudar você a entender esses custos. O Site Recovery agora fornece uma [estimativa de preço de exemplo](https://aka.ms/a2a-cost-estimator) que precifica uma implantação de exemplo com base em um aplicativo de três camadas usando seis VMs com 12 discos HDD Standard e 6 discos SSD Premium. A amostra presume uma alteração de dados de 10 GB por dia para Standard e 20 GB para Premium. Para sua implantação específica, você pode alterar as variáveis para estimar os custos. Você pode especificar o número de VMs, o número e o tipo de discos gerenciados e a taxa total esperada de alteração de dados entre as VMs. Além disso, você pode aplicar um fator de compactação para estimar os custos de largura de banda. [Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o comunicado.

### <a name="support-for-azure-vms-in-zones"></a>Suporte para VMs do Azure em zonas

As Zonas de Disponibilidade do Azure são localizações físicas exclusivas em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Agora você pode habilitar a replicação em uma VM do Azure e definir o destino de failover como uma única instância de VM, uma VM em um conjunto de disponibilidade ou uma VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o comunicado.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Recuperação de desastre para VMs criptografadas

O Site Recovery dá suporte a VMs do Azure criptografadas com o ADE (Azure Disk Encryption) com o aplicativo do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Recuperação de desastre para VMs que usam a rede acelerada

A rede acelerada habilita o SR-IOV (virtualização de E/S de raiz única) em uma VM, melhorando o desempenho de rede. Quando você habilita a replicação em uma VM do Azure, o Site Recovery detecta se a rede acelerada está habilitada. Nesse caso, após o failover, o Site Recovery configura automaticamente a rede acelerada na VM do Azure de réplica de destino para o [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Atualizações automáticas para a extensão do Serviço de Mobilidade

O Site Recovery adicionou uma opção para atualizações automáticas à extensão do Serviço de Mobilidade. A extensão do Serviço de Mobilidade é instalada em cada VM do Azure replicada pelo Site Recovery. Ao habilitar a replicação, selecione se deseja permitir que o Site Recovery gerencie as atualizações para a extensão. As atualizações não exigem uma reinicialização de VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>Suporte para discos SSD Standard

O Azure introduziu discos [SSD (unidades de estado sólido) Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) para fornecer uma solução de armazenamento econômica para aplicativos, como servidores Web que precisam de um desempenho consistente, mas que não têm uma IOPS alta de disco. Eles combinam elementos dos discos SSD Premium e HDD Standard. O Site Recovery fornece recuperação de desastre para VMs do Azure que usam o disco SSD Standard. Por padrão, o tipo de disco é preservado após o failover para a região de destino.

### <a name="support-for-azure-storage-firewall"></a>Suporte para o firewall de armazenamento do Azure

Proteja contas de armazenamento do Azure em um conjunto específico de redes ativando regras de firewall para a conta. Configure contas de armazenamento para negar o tráfego de redes internas e da Internet por padrão e, em seguida, permita acesso ao tráfego em VNETs específicas. O Site Recovery dá suporte à replicação para VMs com discos não gerenciados em contas de armazenamento habilitadas para firewall em uma região secundária. Na região de destino, para discos não gerenciados, você pode selecionar as contas de armazenamento com firewalls habilitados. Você também pode restringir o acesso à conta de armazenamento em cache restringindo o acesso à rede somente à rede na qual as VMs de origem estão localizadas. Observe que é necessário [permitir o acesso](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para serviços confiáveis da Microsoft.

## <a name="q3-2018"></a>T3 2018 

### <a name="linux-support"></a>Suporte para Linux

#### <a name="update-rollup-28"></a>Pacote cumulativo de atualizações 28

O [pacote cumulativo de atualizações 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece uma atualização para os agentes e provedores do Site Recovery. As atualizações adicionam suporte para o Linux da seguinte maneira:

- **Recuperação de desastre de VMs do Azure**: Red Hat Enterprise Linux 6.10; CentOS 6.10; agora há suporte para VMs baseadas no Linux que usam o estilo de partição GGT (tabela de partição GUID) no modo de compatibilidade de BIOS herdado.
- **Recuperação de desastre de servidores físicos/VMs da VMware para o Azure**: Red Hat Enterprise Linux 6.10; CentOS 6.10; agora há suporte para VMs baseadas no Linux que usam o estilo de partição GGT (tabela de partição GUID) no modo de compatibilidade de BIOS herdado.

#### <a name="update-rollup-27"></a>Pacote cumulativo de atualizações 27

O [pacote cumulativo de atualizações 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece uma atualização para os agentes e provedores do Site Recovery. As atualizações adicionam suporte para o Linux da seguinte maneira:

- **Recuperação de desastre de VMs do Azure**: Red Hat Enterprise Linux 7.5
- **Recuperação de desastre de servidores físicos/VMs da VMware para o Azure**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Suporte para VMs do Azure em execução no Windows Server 2016

As VMs do Azure em execução no Windows Server 2016 podem ser replicadas entre regiões do Azure com o Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Suporte para VMs do Azure que executam os Espaços de Armazenamento Diretos

Os [Espaços de Armazenamento Diretos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (disponíveis no Windows Server 2016 em diante) agrupam as unidades em um pool de armazenamento e, em seguida, usam a capacidade do pool para criar espaços de armazenamento. Os Espaços de Armazenamento podem ser usados em uma VM autônoma, em um [cluster convidado de VMs do Azure](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) usando o armazenamento local em cada nó de cluster ou em um armazenamento compartilhado em todo o cluster.

## <a name="next-steps"></a>Próximas etapas

Mantenha-se atualizado com nossas atualizações na página [Atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).


