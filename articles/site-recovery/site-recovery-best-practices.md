<properties
	pageTitle="Preparar-se para a implantação de Recuperação de Site | Microsoft Azure"
	description="Este artigo descreve como se preparar para implantar a replicação com o Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/08/2016"
	ms.author="raynew"/>

# Preparar-se para a implantação do Azure Site Recovery

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

## Visão geral

O Azure Site Recovery dá suporte à replicação de servidores físicos e máquinas virtuais do Hyper-V e VMware para o Azure ou em um datacenter secundário. Este artigo descreve como se preparar para a implantação do Azure Site Recovery para cada um desses cenários de replicação.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Requisitos de implantação para replicação do Hyper-V

A tabela resume os requisitos de implantação geral para replicação do Hyper-V para Azure (com e sem o VMM) e um site secundário. A tabela ajuda você a compreender e a comparar requisitos gerais para cada cenário de replicação. Também há um link para os pré-requisitos de implantação detalhados.

**Replicar no Azure (com VMM)** | **Replicar para o Azure (sem VMM)** | **Replicar para o site secundário (com VMM)**
---|---|---
**VMM**: pelo menos um servidor VMM executando no System Center 2012 R2. O servidor do VMM deve ter pelo menos uma nuvem que contém um ou mais grupos de hosts do VMM.<br/><br/> **Hyper-V**: um ou mais servidores de host do Hyper-V no datacenter local executando pelo menos Windows Server 2012 R2. O servidor Hyper-V deve estar localizado em um grupo de hosts em uma nuvem VM.<br/><br/> **Máquinas virtuais**: você precisará de pelo menos uma VM no servidor de origem Hyper-V. As VMs que estão replicando no Azure devem atender aos [pré-requisitos de máquina virtual do Azure](#azure-virtual-machine-requirements).<br/><br/> **Conta do Azure**: você precisará de uma conta e assinatura do [Azure](https://azure.microsoft.com/). <br/><br/> **Armazenamento do Azure**: você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Mapeamento de rede**: Configurar mapeamento de rede para que todas as máquinas que sofrerem failover na mesma rede do Azure possam se conectar entre si, independentemente do plano de recuperação em vigor. Se houver um gateway de rede na rede Azure de destino, as máquinas virtuais também podem se conectar a máquinas virtuais locais. Se você não configurar o mapeamento de rede, apenas as máquinas que sofrerem failover no mesmo plano de recuperação poderão se conectar.<br/><br/> **Provedores/agentes**: durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM e o agente de serviços de recuperação do Azure em servidores host Hyper-V. O provedor se comunica com o Azure Site Recovery. O agente lida com a replicação de dados entre servidores Hyper-V de origem e destino. Nada é instalado em máquinas virtuais.<br/><br/> **Conectividade com a Internet**: servidor do VMM e hosts Hyper-V.<br/><br/> **Conectividade do provedor**: se o provedor se conectar ao Site Recovery por meio de um proxy, você precisará certificar-se de que o proxy pode acessar as URLs do Site Recovery.<br/><br/> [Pré-requisitos de implantação detalhados](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**: pelo menos um servidor Hyper-V nos sites de origem e de destino executando pelo menos Windows Server 2012 R2.<br/><br/> **Máquinas virtuais**: pelo menos uma VM no servidor de origem Hyper-V. As VMs replicando para o Azure devem estar de acordo com os [Pré-requisitos de máquina virtual do Azure](#azure-virtual-machine-requirements)<br/><br/> **Conta do Azure**: você precisará de uma conta e assinatura do [Azure](https://azure.microsoft.com/).<br/><br/> **Armazenamento do Azure**: você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados no Azure.<br/><br/> **Provedores/agentes**: durante a implantação, você instalará o provedor do Azure Site Recovery e o agente de serviços de recuperação do Azure em servidores host Hyper-V ou clusters. Nada é instalado em máquinas virtuais.<br/><br/> **Conectividade com a Internet**: hosts do Hyper-V.<br/><br/> **Conectividade do provedor**: se o provedor se conectar por meio de um proxy, você precisará certificar-se de que o proxy pode acessar URLs do Site Recovery.<br/><br/> [Pré-requisitos de implantação detalhados](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**: o servidor VMM de origem deve ter pelo menos uma nuvem que contém um ou mais grupos de hosts do VMM. As nuvens devem ter o perfil de funcionalidade do Hyper-V definido. <br/><br/>* * Hyper-V * *: um ou mais servidores do Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012 com as atualizações mais recentes. O servidor Hyper-V deve estar localizado em um grupo de hosts em uma nuvem VMM.<br/><br/> **Máquinas virtuais**: pelo menos uma VM na fonte de nuvem do VMM. <br/><br/> **Mapeamento de rede**: Configurar mapeamento de rede para que as máquinas virtuais sejam conectadas a redes apropriadas após o failover, e máquinas virtuais de réplica sejam colocadas idealmente em servidores host Hyper-V de destino. Se você não configurar o mapeamento de rede, as máquinas replicadas não serão conectadas a nenhuma rede de VM após o failover.<br/><br/> **Mapeamento de armazenamento**: você pode definir opcionalmente o mapeamento de armazenamento para certificar-se de que as máquinas virtuais estejam idealmente conectadas ao armazenamento após o failover (por padrão, a VM de réplica será armazenada no local indicado no servidor Hyper-V de destino).<br/><br/> **Replicação SAN** Se você quiser replicar entre dois sites do VMM local com replicação SAN, você pode usar o ambiente de SAN existente. Exibição [com suporte para matrizes de SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> **Provedores/agentes**: durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM para se comunicar com o Azure Site Recovery. A replicação ocorre entre servidores Hyper-V de origem e destino através da LAN/VPN.<br/><br/> **Conectividade com a Internet**: somente servidor do VMM.<br/><br/> **Conectividade do provedor**: se o provedor se conectará por meio de um proxy, você precisará certificar-se de que as URLs do Site Recovery sejam acessíveis.<br/><br/> [Pré-requisitos de implantação detalhados](site-recovery-vmm-to-vmm.md#before-you-start)


## Requisitos de implantação para a replicação de servidores físicos e máquinas virtuais VMware

A tabela resume os requisitos para replicar máquinas virtuais VMware e servidores físicos Windows/Linux do Azure e um site secundário.

>[AZURE.NOTE] Você pode replicar máquinas virtuais VMware e servidores físicos no Azure usando um modelo [aprimorado](site-recovery-vmware-to-azure-classic.md) de implantação, ou com um modelo [herdado](site-recovery-vmware-to-azure-classic-legacy.md) que foi usado para implantações mais antigas. A tabela a seguir inclui os requisitos de implantação para cada modelo.

**Replicar para o Azure (Avançado)** | **Replicar para o Azure (herdado)** | **Replicar para o site secundário**
---|---|---
**Servidor de gerenciamento local**: no seu site local será necessário um servidor dedicado que atuará como o servidor de gerenciamento no site local. Todos os componentes do Site Recovery estão instalados neste servidor.<br/><br/> **Servidores adicionais do processo**: um servidor de processo é instalado por padrão no servidor de gerenciamento, mas você pode, opcionalmente, instalar servidores de processo local adicionais para dimensionar sua implantação.<br/><br/> **VMware vCenter/ESXi**: se você estiver replicando máquinas virtuais VMware (ou deseja transferir servidores físicos de volta), será necessário um vSphere ESX/ESXi no qual as VMs estão localizadas. Recomendamos um servidor vCenter para gerenciar os hosts ESXi.</br><br/> **Failback**: você precisa de um ambiente VMware para failback do Azure, mesmo se você estiver replicando servidores físicos. Além disso, será necessário configurar um servidor de processo como uma VM do Azure e se estiver fazendo failback de grandes volumes de tráfego, você talvez queira configurar um servidor de destino mestre local adicional. [Saiba mais sobre a](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **conta do Azure**: você precisará de uma conta e assinatura do [Azure](https://azure.microsoft.com/).<br/><br/> **Armazenamento do Azure**: você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Rede virtual do Azure**: você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. Falha após o failover, você terá uma conexão de VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local.<br/><br/> **Máquinas protegidas**: pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux. Durante a implantação, o serviço de mobilidade é instalado em cada computador que você deseja replicar.<br/><br/> **Conectividade**: se o servidor de gerenciamento se conectará ao Site Recovery por meio de um proxy, você precisará certificar-se de que o servidor proxy possa se conectar a URLs específicas.<br/><br/> [Pré-requisitos de implantação detalhados](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment). | **Site primário**: você precisará configurar um servidor de processo.<br/><br/> **Failback**: você precisa de um ambiente VMware para failback do Azure, mesmo se você estiver replicando servidores físicos. No site local, você precisará configurar um servidor vContinuum e um servidor de destino mestre. No Azure, você precisará configurar um servidor de processo. [Saiba mais sobre a](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **conta do Azure**: você precisará de uma conta e assinatura do [Azure](https://azure.microsoft.com/).<br/><br/> **Armazenamento do Azure**: você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Infraestrutura de VMs do Azure**: você precisará configurar um servidor de configuração e um servidor de destino mestre como VMs do Azure.<br/><br/> **Rede virtual do Azure**: você precisará de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implantados. As VMs do Azure serão conectadas à rede após o failover.<br/><br/> **Máquinas protegidas**: pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux. Durante a implantação, o serviço de mobilidade é instalado em cada computador que você deseja replicar.<br/><br/> **Conectividade**: se o servidor de gerenciamento se conectará ao Site Recovery por meio de um proxy, você precisará certificar-se de que o servidor proxy possa se conectar a URLs específicas.<br/><br/> [Pré-requisitos de implantação detalhados](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start). | **Site primário**: um servidor Windows dedicado (físico ou máquina virtual VMware.<br/><br/> **Site secundário**: configuração dedicada e servidores de destino mestres.<br/><br/> **Máquinas protegidas**: pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux. Durante a implantação, o agente Unificado será instalado em cada computador.





## Requisitos de máquina virtual do Azure

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. Será necessário verificar se as máquinas virtuais locais que você deseja proteger atendem aos requisitos do Azure.


**Recurso** | **Suporte** | **Detalhes**
---|---|---
Sistema operacional host Hyper-V | Windows Server 2012 R2 | A verificação de pré-requisitos falhará se não houver suporte
Sistema de operacional do hipervisor VMware | Executando um sistema operacional com suporte | [Detalhes](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operacional convidado | Para replicação do Hyper-V para o Azure, a Recuperação de Site dá suporte a todos os sistemas operacionais [com suporte pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico, verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) do Windows e Linux | A verificação de pré-requisitos falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do disco do sistema operacional | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Contagem do disco do sistema operacional | 1 | A verificação de pré-requisitos falhará se não houver suporte.
Contagem de disco de dados | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do VHD do disco de dados | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Adaptadores de rede | Há suporte para vários adaptadores |
Endereço IP estático | Com suporte | Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure. Observe que não há suporte para endereço IP estático para uma máquina virtual Linux em execução no Hyper-v. 
Disco iSCSI | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
VHD compartilhado | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Disco FC | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Formato de disco rígido| VHD <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
Nome da máquina virtual| Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
Tipo de máquina virtual | <p>Geração 1</p> <p>Geração 2 - Windows</p> | Há suporte para a máquina virtual de 2ª geração com o tipo de disco do sistema operacional de um disco básico que inclui os volumes de dados 1 ou 2 com o formato de disco como VHDX, que é menor do que 300GB. Não há suporte para as máquinas virtuais Linux Geração 2. [Para obter mais informações](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Otimizando sua implantação

Use as dicas a seguir para ajudá-lo a otimizar e dimensionar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e realizar de forma efetiva o failover de uma máquina virtual de aproximadamente 32 TB.
- **Limites de plano de recuperação**: a Recuperação de Site pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços em nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: Leia sobre [planejamento de capacidade](site-recovery-capacity-planner.md) para Recuperação de Site.
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
	- **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
	- **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
	- **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta. Além disso, se você estiver replicando para a Azure Site Recovery usando o agente de Backup do Azure. Você pode configurar a limitação para esse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: se você quiser medir o objetivo do tempo de recuperação (RTO) que você pode esperar com Recuperação de Site, sugerimos a execução de um teste de failover e a exibição dos trabalhos de Recuperação de Site a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.





## Próximas etapas

Depois de aprender e comparando os requisitos de implantação geral, você pode ler os pré-requisitos detalhados e iniciar a implantação de cada cenário.

- [Replique as máquinas virtuais VMware para o Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicar servidores físicos para o Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicar servidor Hyper-V em nuvens do VMM para o Azure](site-recovery-vmm-to-azure.md)
- [Replicar máquinas virtuais Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicar máquinas virtuais do Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
- [Replicar máquinas virtuais do Hyper-V para um site secundário com SAN](site-recovery-vmm-san.md)
- [Replicar máquinas virtuais do Hyper-V com um único servidor VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0309_2016-->