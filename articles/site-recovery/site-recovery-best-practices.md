---
title: "Práticas recomendadas do Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve as práticas recomendadas para a implantação do Azure Site Recovery"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Preparação para a implantação do Azure Site Recovery

Este artigo descreve como se preparar para a implantação do Azure Site Recovery.

## <a name="protecting-hyper-v-virtual-machines"></a>Protegendo as máquinas virtuais Hyper-V

Você tem algumas opções de implantação para proteger máquinas virtuais Hyper-V. É possível replicar VMs Hyper-V locais no Azure ou em um datacenter secundário. Há requisitos diferentes para cada implantação.

**Requisito** | **Replicar no Azure (com VMM)** | **Replicar VMs Hyper-V no Azure (sem VMM)** | **Replicar VMs Hyper-V no site secundário (com VMM)** | **Detalhes**
---|---|---|---|---
**VMM** | VMM em execução no System Center 2012 R2 <br/><br/>Pelo menos uma nuvem do VMM que contém um ou mais grupos de hosts do VMM. | ND | Servidores VMM nos sites primário e secundário em execução pelo menos no System Center 2012 SP1 com as atualizações mais recentes. <br/><br/> Pelo menos uma nuvem em cada servidor VMM. As nuvens devem ter o perfil de capacidade do Hyper-V definido.<br/><br/> A nuvem de origem deve ter pelo menos um grupo de hosts do VMM. | Opcional. Não é necessário ter o System Center VMM implantado para replicar máquinas virtuais Hyper-V no Azure, mas, se você o fizer, precisará se certificar de que o servidor VMM esteja configurado adequadamente. Isso inclui verificar se você está executando a versão correta do VMM e se as nuvens estão configuradas.
**Hyper-V** | Pelo menos um servidor de host Hyper-V no site local executando o Windows Server 2012 R2 ou posterior | Pelo menos um servidor Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012, com as atualizações mais recentes instaladas e conectado à Internet.<br/><br/> Os servidores Hyper-V devem estar em um grupo de hosts em uma nuvem VMM. | Pelo menos um servidor Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012, com as atualizações mais recentes instaladas e conectado à Internet.<br/><br/> Os servidores Hyper-V devem estar localizados em um grupo de hosts em uma nuvem VMM. |
**Máquinas virtuais** | Pelo menos uma VM no servidor de host Hyper-V de origem | Pelo menos uma VM no servidor de host Hyper-V na nuvem VMM de origem | Pelo menos uma VM no servidor de host Hyper-V na nuvem VMM de origem. |  As VMs que estão replicando no Azure devem estar em conformidade com os pré-requisitos de máquina virtual do Azure
**Conta do Azure** | Você precisará de uma conta do Azure e de uma assinatura do serviço Site Recovery. | Você precisará de uma conta do Azure e de uma assinatura do serviço Site Recovery. | ND | Se você não tiver uma conta, comece com uma avaliação gratuita.
**Armazenamento do Azure** | Você precisará de uma assinatura para uma conta de Armazenamento do Azure com replicação geográfica habilitada. | Você precisará de uma assinatura para uma conta de Armazenamento do Azure com replicação geográfica habilitada. | ND | A conta deve estar na mesma região que o cofre do Azure Site Recovery e estar associada à mesma assinatura.
**Rede** | Configure o mapeamento de rede para assegurar que todas as máquinas que passarem por failover na mesma rede do Azure possam se conectar entre si, independentemente do plano de recuperação a que cada uma delas pertença. Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais. Se você não configurar o mapeamento de rede, apenas as máquinas que sofrerem failover no mesmo plano de recuperação poderão se conectar. | ND |  <br/><br/>Configure o mapeamento de rede para assegurar que as máquinas virtuais estejam conectadas às redes apropriadas após o failover e que as máquinas virtuais de réplica sejam colocadas da forma ideal em servidores de host Hyper-V. Se você não configurar o mapeamento de rede, as máquinas replicadas não serão conectadas a nenhuma rede de VM após o failover. |  Para configurar o mapeamento de rede com o VMM, será necessário garantir que as redes lógicas do VMM e as redes de VM estejam configuradas corretamente.
**Provedores e agentes** | Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM. Em servidores do host Hyper-V localizados em nuvens do VMM, você instalará o agente dos Serviços de Recuperação do Azure. | Durante a implantação, você instalará o provedor do Azure Site Recovery e o agente de Serviços de Recuperação do Azure no servidor ou cluster do host Hyper-V.| Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM. Em servidores do host Hyper-V localizados em nuvens do VMM, você instalará o agente dos Serviços de Recuperação do Azure. | Os provedores e agentes se conectam à Recuperação de Site pela Internet usando uma conexão HTTPS criptografada. Não é necessário adicionar exceções de firewall ou criar um proxy específico para a conexão do Provedor.
**Conectividade com a Internet** | Somente os servidores do VMM precisam de uma conexão com a internet | Somente os servidores host do Hyper-V precisam de uma conexão com a internet | Somente servidores do VMM precisam de uma conexão com a internet | Máquinas virtuais não precisam de qualquer instalação e não se conectam diretamente à internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Proteger máquinas virtuais VMware ou servidores físicos

Há algumas opções de implantação para a proteção de máquinas virtuais VMware ou de servidores físicos com Windows/Linux. É possível replicá-las no Azure ou em um datacenter secundário. Há requisitos diferentes para cada implantação.

**Requisito** | **Replicar VMs VMware/servidores físicos para o Azure** | * **Replicar VMs VMware/servidores físicos para um site secundário**  
---|---|---
**Site primário** | **Servidor de processo**: um servidor dedicado com Windows (físico ou virtual) | **Servidor de processo**: um servidor dedicado com Windows (físico ou máquina virtual VMware)<br/><br/>  
**Site local secundário** | ND | **Servidor de configuração**: um servidor dedicado com Windows (físico ou virtual) <br/><br/> **Servidor de destino mestre**: um servidor dedicado (físico ou virtual). Configure usando o Windows para proteger máquinas com Windows, ou usando o Linux para proteger máquinas com Linux.
**As tabelas** | **Assinatura**: você precisará de uma assinatura do serviço Site Recovery. <br/><br/> **Conta de armazenamento**: você precisará de uma conta de armazenamento com replicação geográfica habilitada. A conta deve estar na mesma região que o cofre do Site Recovery e estar associada à mesma assinatura. <br/><br/> **Servidor de configuração**: você precisará configurar o servidor de configuração como uma VM do Azure <br/><br/> **Servidor de destino mestre**: você precisará configurar o servidor de destino mestre como uma VM do Azure <br/><br/> Configure usando o Windows para proteger máquinas com Windows, ou usando o Linux para proteger máquinas com Linux.<br/><br/> **Rede virtual do Azure**: você precisará de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implantados. Ela deve estar na mesma assinatura e na mesma região que o cofre do Azure Site Recovery | ND  
**Máquinas virtuais/servidores físicos** | Pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux.<br/><br/>Durante a implantação, o serviço de Mobilidade será instalado em cada computador| Pelo menos uma VM VMware ou servidor físico com Windows/Linux.<br/><br/> Durante a implantação, o Agente Unificado será instalado em cada máquina.




## <a name="azure-virtual-machine-requirements"></a>Requisitos de máquina virtual do Azure

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. Será necessário verificar se as máquinas virtuais locais que você deseja proteger atendem aos requisitos do Azure.


## <a name="optimizing-your-deployment"></a>Otimizando sua implantação

Use as dicas a seguir para ajudá-lo a otimizar e dimensionar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e realizar de forma efetiva o failover de uma máquina virtual de aproximadamente&32; TB.
- **Limites de plano de recuperação**: o Site Recovery pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços de nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: planeje o desempenho e o dimensionamento.
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
    - **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed.
    - **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
    - **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o Windows QoS com uma política baseada no endereço IP de destino e na porta.  Além disso, se você estiver replicando para a Azure Site Recovery usando o agente de Backup do Azure. Você pode configurar a limitação para esse agente.
- **RTO**: se você quiser medir o objetivo do tempo de recuperação (RTO) que você pode esperar com Site Recovery, sugerimos a execução de um teste de failover e a exibição dos trabalhos de Site Recovery a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.

