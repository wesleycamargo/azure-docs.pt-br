---
title: "Preparar-se para a implantação de Recuperação de Site | Microsoft Docs"
description: "Este artigo descreve como se preparar para implantar a replicação com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparar-se para a implantação do Azure Site Recovery

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

Este artigo resume os requisitos de alto nível para cada cenário de replicação do Site Recovery e fornece links para passo a passos de implantação detalhados.  

Poste comentários no final do artigo ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelos de implantação do Azure

O Azure tem dois [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modo Azure Resource Manager e o modelo clássico. O Azure também tem dois portais: o [portal clássico do Azure](https://manage.windowsazure.com/), que dá suporte ao modelo de implantação clássico, e o [Portal do Azure](https://ms.portal.azure.com/), com suporte aos dois modelos de implantação.

Novos cenários do Site Recovery devem ser implantados no [portal do Azure](https://portal.azure.com). Esse portal fornece novos recursos e uma experiência avançada de implantação. Os cofres podem ser mantidos no portal clássico, mas não é possível criar novos cofres.


## <a name="deployment-scenarios"></a>Cenários de implantação

Isto é o que você pode replicar com o Site Recovery.

| **Implantação** | **Detalhes** | **Portal do Azure** | **Portal clássico** | **Implantação do PowerShell** |
| --- | --- | --- | --- | --- |
| [VMware no Azure](site-recovery-vmware-to-azure.md) | Replicar VMs VMware locais para o Armazenamento do Azure | Use o Resource Manager ou o armazenamento e as redes clássicas. Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | Somente modo de manutenção. Não é possível criar novos cofres. | Não há suporte no momento. |
| [Servidores físicos para o Azure](site-recovery-vmware-to-vmware.md) | Replicar servidores físicos do Windows e Linux para armazenamento do Azure | Use o Resource Manager ou o armazenamento e as redes clássicas. Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | Somente modo de manutenção. Não é possível criar novos cofres.  |
| [VMs Hyper-V em nuvens do VMM para o Azure](site-recovery-vmm-to-azure.md) | Replicar VMs Hyper-V locais em nuvens do VMM para o armazenamento do Azure.<br/><br/> | Use o Resource Manager ou o armazenamento e as redes clássicas. Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos.   | Somente modo de manutenção. Não é possível criar novos cofres. | Suportado |
| [VMs do Hyper-V para Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md) | Replicar VMs Hyper-V locais para o armazenamento do Azure. | Use o Resource Manager ou o armazenamento e as redes clássicas. Executar failover para VMs clássicas ou baseadas no Gerenciador de recursos. | Somente modo de manutenção. Não é possível criar novos cofres. | Suportado |
| Servidores de VMs VMware/físicos para o site secundário (site-recovery-vmware-to-vmware.md) | Replicar VMs VMware ou servidores físicos Windows/Linux para um site secundário.<br/><br/> [Baixe o guia de ajuda](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) no guia do usuário do InMage Scout. | Não disponível no Portal do Azure | Baixe o InMage Scout em um cofre do Site Recovery. | Sem suporte |
| [VMs do Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md) | Replicar as VMs do Hyper-V em nuvens de VMM para uma nuvem secundária  | A replicação usa a Réplica do Hyper-V padrão. Não há suporte para a rede SAN. | Replicar usando a Réplica do Hyper-V ou a [replicação da rede SAN](site-recovery-vmm-san.md) | Suportado |

## <a name="requirements-for-replication-to-azure"></a>Requisitos de replicação para o Azure

| **Requisito** | **Detalhes** |
| --- | --- |
| **Conta do Azure** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. |
| **Armazenamento do Azure** | Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover. Você precisa de uma [conta de armazenamento do Azure](../storage/storage-introduction.md).<br/><br/>No portal do Azure, use o armazenamento [GRS](../storage/storage-redundancy.md#geo-redundant-storage) ou LRS. O portal clássico dava suporte apenas para GRS.<br/><br/> Se você replicar VMs VMware ou servidores físicos no portal do Azure, haverá suporte para o armazenamento premium.<br/><br/>  |
| **Rede do Azure** | Você precisa de uma rede do Azure à qual as VMs do Azure serão conectadas <br/><br/> No portal do Azure, você pode usar uma rede clássica ou do Resource Manager.<br/><br/> A rede deve estar na mesma região do que o cofre.<br/><br/> Se você replicar do VMM para o Azure, você configurará o [mapeamento de rede](site-recovery-network-mapping.md) entre as redes de VM do VMM e as redes do Azure, para garantir que as VMs do Azure se conectem a redes apropriadas após o failover. |
| **Configuração local** |**VMs VMware**: um computador local que executa componentes do Site Recovery. Além disso, você precisa de hosts VMware vSphere/servidor vCenter e das VMs que você deseja replicar. [Saiba mais](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Servidores físicos**: um computador local que executa componentes do Site Recovery e os servidores físicos que você deseja replicar. [Saiba mais](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Se você desejar executar [failback](site-recovery-failback-azure-to-vmware.md) dos servidores físicos no Azure, você precisará de uma infraestrutura do VMware para fazer isso.<br/><br/> **VMs Hyper-V**: um servidor VMM e hosts Hyper-V que contêm as VMs que você deseja replicar. [Saiba mais](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Para replicar VMs Hyper-V sem o VMM, você precisa apenas dos hosts do Hyper-V. [Saiba mais](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Computadores protegidos** | Computadores protegidos que serão replicados para o Azure devem cumprir os [pré-requisitos do Azure](#azure-virtual-machine-requirements) descritos abaixo. |

## <a name="requirements-for-replication-to-a-secondary-site"></a>Requisitos de replicação para um site secundário

| **Requisito** | **Detalhes** |
| --- | --- |
| **As tabelas** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. |
| **Configuração local** |**VMs VMware**: no site primário, é necessário um servidor em processo para o cache, compactação e criptografia dos dados de replicação. No site secundário, você precisa de um servidor de configuração para gerenciar e monitorar a implantação e de um servidor de destino mestre. Também recomendamos o uso de um servidor vContinuum para facilitar o gerenciamento. Além disso, você precisa de um ou mais hosts VMware vSphere e, opcionalmente, um servidor vCenter. [Saiba mais](site-recovery-vmware-to-vmware.md)<br/><br/> **VMs Hyper-V em nuvens do VMM**: servidores VMM e hosts Hyper-V que contém as VMs que você deseja replicar. [Saiba mais](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Rede (VMM para o VMM)** | Se você replicar do VMM para o VMM, configure o [mapeamento de rede](site-recovery-network-mapping.md) para garantir que as VMs de réplica estão conectadas às redes apropriadas após o failover e são colocadas de forma ideal em hosts Hyper-V. |

## <a name="url-access"></a>Acesso à URL

Essas URLs devem estar disponíveis no VMware, no VMM e nos servidores host Hyper-V.

**URL** | **VMM no VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware no Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir
``*.backup.windowsazure.com`` | Não requerido | Permitir | Permitir | Permitir
``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir
``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir
``*.blob.core.windows.net`` | Não requerido | Permitir | Permitir | Permitir
``https://www.msftncsi.com/ncsi.txt`` | Permitir | Permitir | Permitir | Permitir
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Não requerido | Não requerido | Não requerido | Permitir download do SQL
``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir
``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR



## <a name="azure-virtual-machine-requirements"></a>Requisitos de máquina virtual do Azure

Você pode implantar o Site Recovery para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte no Azure. Isso inclui a maioria das versões do Windows e do Linux. As máquinas virtuais locais que você deseja replicar deve estar em conformidade com os requisitos do Azure.

**Recurso** | **Requisitos** | **Detalhes**
--- | --- | ---
**Host Hyper-V** | Deve executar o Windows Server 2012 R2 ou posterior | A verificação de pré-requisitos falhará se o sistema operacional não tiver suporte
**Hipervisor VMware** | Sistema operacional com suporte | [Verifique os requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
**Adaptadores de rede** | Há suporte para vários adaptadores |
**Endereço IP estático** | Suportado | Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure.<br/><br/> Não há suporte para o endereço IP estático em uma **VM do Linux em execução no Hyper-V**.
**Disco iSCSI** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | As VMs da Geração 2 com um tipo de disco básico de sistema operacional, que inclui um ou dois volumes de dados formatados como VHDX e há suporte para menos de 300 GB.<br/><br/>. Não há suporte para VMs do Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="deployment-optimization"></a>Otimização da implantação

Use as dicas a seguir para otimizar e escalar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 64 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e executar failover de forma efetiva de uma máquina virtual de aproximadamente 64 TB.
- **Limites de plano de recuperação**: o Site Recovery pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços de nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: leia sobre o [planejamento de capacidade](site-recovery-capacity-planner.md) para o Site Recovery.
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
- **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
- **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
- **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta.  Além disso, se você estiver replicando para o Azure Site Recovery usando o agente de Backup do Azure, poderá configurar a limitação desse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: para medir o RTO (Objetivo do Tempo de Recuperação) que você pode esperar com o Site Recovery, sugerimos executar um failover de teste e exibir os trabalhos do Site Recovery a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.



## <a name="next-steps"></a>Próximas etapas
Depois de examinar os requisitos de implantação gerais, leia os pré-requisitos detalhados e implante um cenário.

* [Replique as máquinas virtuais VMware para o Azure](site-recovery-vmware-to-azure.md)
* [Replicar servidores físicos para o Azure](site-recovery-vmware-to-azure.md)
* [Replicar servidor Hyper-V em nuvens do VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar máquinas virtuais Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar máquinas virtuais do Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
* [Replicar máquinas virtuais do Hyper-V com um único servidor VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


