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
	ms.date="03/29/2016"
	ms.author="raynew"/>

# Preparar-se para a implantação do Azure Site Recovery

Leia este artigo para obter uma visão de alto nível dos requisitos de implantação para cada cenário de replicação com suporte do serviço Azure Site Recovery. Depois que você ler os requisitos gerais de cada link de cenário para obter detalhes específicos sobre a implantação na seção pré-requisitos de cada artigo de implantação.

Depois de ler este artigo, publique quaisquer comentários ou perguntas ao final dele ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR está centrada em soluções que mantêm os dados comerciais seguros e passíveis de recuperação, e as cargas de trabalho continuamente disponíveis, mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. A Recuperação de Site pode ser usada em vários cenários e pode proteger diversas cargas de trabalho. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)


## Requisitos para replicar as máquinas virtuais do Hyper-V

**Componente** | **Replicar no Azure (com VMM)** | **Replicar para o Azure (sem VMM)** | **Replicar para o site secundário (com VMM)**
---|---|---|---
**VMM** | Um ou mais servidores VMM em execução no System Center 2012 R2. O servidor VMM deve ter pelo menos uma nuvem que contenha um ou mais grupos de hosts do VMM. | Não aplicável | Pelo menos um servidor VMM em execução no System Center 2012 R2. Recomendamos um servidor VMM em cada site. O servidor VMM deve ter pelo menos uma nuvem que contenha um ou mais grupos de hosts do VMM. As nuvens devem ter o perfil de recursos do Hyper-V definido. 
**Hyper-V** | Um ou mais servidores de host do Hyper-V no datacenter local executando pelo menos o Windows Server 2012 R2. O servidor Hyper-V deve estar localizado em um grupo de hosts em uma nuvem VMM. | Um ou mais servidores Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012 R2. | Um ou mais servidores Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012 com as atualizações mais recentes. O servidor Hyper-V deve estar localizado em um grupo de hosts em uma nuvem VMM.
**Máquinas virtuais** | Você precisará de pelo menos uma VM no servidor Hyper-V de origem. As VMs que estão replicando no Azure devem estar em conformidade com os [pré-requisitos de máquina virtual do Azure](#azure-virtual-machine-requirements). <br> Instalar ou atualizar [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) (Serviços de integração) na máquina virtual usando as etapas fornecidas [aqui](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Pelo menos uma VM no servidor Hyper-V de origem. As VMs que estão replicando no Azure devem estar em conformidade com os [pré-requisitos de máquina virtual do Azure](#azure-virtual-machine-requirements). <br> Instalar ou atualizar [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) (Serviços de integração) na máquina virtual usando as etapas fornecidas [aqui](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Pelo menos uma VM na nuvem VMM de origem. <br> Instalar ou atualizar [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) (Serviços de integração) na máquina virtual usando as etapas fornecidas [aqui](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4).
**Conta do Azure** | Você precisará de uma conta e uma assinatura do [Azure](https://azure.microsoft.com/). | Não aplicável | Você precisará de uma conta e uma assinatura do [Azure](https://azure.microsoft.com/).
**Armazenamento do Azure** | Você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover. | Não aplicável | Você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.
**Provedores/Agentes** | Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores VMM e o agente Serviços de Recuperação do Azure nos servidores host Hyper-V. O provedor se comunica com o Azure Site Recovery. O agente lida com a replicação de dados entre servidores Hyper-V de origem e destino. Nada é instalado nas VMs. | Durante a implantação, você instalará o provedor do Azure Site Recovery e o agente de Serviços de Recuperação do Azure em um servidor ou cluster do host Hyper-V. Nada é instalado nas VMs. | Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM para se comunicar com o Azure Site Recovery. A replicação ocorre entre os servidores Hyper-V de origem e destino através da LAN/VPN.
**Conectividade do provedor/agente** | Se o provedor se conectar ao serviço Recuperação de Site por meio de um proxy, você precisará se certificar de que o proxy pode acessar as URLs da Recuperação de Site. | Se o provedor se conectar à Recuperação de Site por meio de um proxy, você precisará se certificar de que o proxy pode acessar as URLs da Recuperação de Site. | Se o provedor se conectar à Recuperação de Site por meio de um proxy, você precisará se certificar de que o proxy pode acessar as URLs da Recuperação de Site.
**Conectividade com a Internet** | Do servidor do VMM e dos hosts do Hyper-V. | Dos hosts do Hyper-V. | Somente no servidor do VMM.
**Mapeamento de rede** | Configure o mapeamento de rede para que todas as máquinas que passarem por failover na mesma rede do Azure possam se conectar entre si, independentemente do plano de recuperação a que cada uma delas pertença. Se houver um gateway de rede na rede Azure de destino, as máquinas virtuais também poderão se conectar às máquinas virtuais locais. Se você não configurar o mapeamento de rede, apenas as máquinas que sofrerem failover no mesmo plano de recuperação poderão se conectar. | Não aplicável | Configure o mapeamento de rede para que as máquinas virtuais sejam conectadas às redes apropriadas após o failover e as máquinas virtuais de réplica sejam colocadas de modo ideal em servidores host Hyper-V de destino. Se você não configurar o mapeamento de rede, as máquinas replicadas não serão conectadas a nenhuma rede de VM após o failover.
**Mapeamento de armazenamento** | Não aplicável | Não aplicável | Como alternativa, você pode definir o mapeamento de armazenamento para se certificar de que as máquinas virtuais estejam conectadas de modo ideal ao armazenamento após o failover (por padrão, a VM de réplica será armazenada no local indicado no servidor Hyper-V de destino).
**Replicação SAN** | Não aplicável | Não aplicável | Se você quiser replicar entre dois sites VMM locais usando a replicação SAN, poderá usar o ambiente SAN existente. Veja [matrizes SAN com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
**Mais informações** | [Pré-requisitos de implantação detalhados](site-recovery-vmm-to-azure.md#before-you-start) | [Pré-requisitos de implantação detalhados](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | [Pré-requisitos de implantação detalhados](site-recovery-vmm-to-vmm.md#before-you-start)




## Requisitos para a replicação de servidores físicos e máquinas virtuais VMware

A tabela resume os requisitos para replicar máquinas virtuais VMware e servidores físicos Windows/Linux do Azure e um site secundário.

>[AZURE.NOTE] Você pode replicar máquinas virtuais VMware e servidores físicos no Azure usando um modelo [aprimorado](site-recovery-vmware-to-azure-classic.md) de implantação ou com um modelo [herdado](site-recovery-vmware-to-azure-classic-legacy.md) que foi usado para implantações mais antigas. A tabela a seguir inclui os requisitos de implantação para o modelo aprimorado.

**Componente** | **Replicar para o Azure (Avançado)** | **Replicar para o site secundário**
---|---|---
**Site primário local** | Você instala um servidor de gerenciamento que executa todos os componentes da Recuperação de Site (configuração, processo, destino mestre). | Você instala um servidor de processo para o caching, compactação e criptografia de dados de replicação antes de enviá-lo para o site secundário. Você pode instalar servidores adicionais de processo para balanceamento de carga ou tolerância a falhas. 
**Site secundário local** | Não aplicável | Você instala um servidor de configuração único que é usado para configurar, gerenciar e monitorar a implantação. <br/><br>Recomendamos que você instale um servidor vContinuum para o gerenciamento fácil do servidor de configuração. <br/><br/>Você precisará configurar o servidor de destino mestre como uma VM em execução no servidor secundário vSphere. 
**VMware vCenter/ESXi** | Se você estiver replicando máquinas virtuais VMware (ou deseja executar failback em servidores físicos) no seu site primário, será necessário um vSphere ESX/ESXi em seu site primário. Também recomendamos um servidor vCenter para gerenciar os hosts ESXi. | No site primário e secundário, você precisará de um ou mais hosts VMware ESXi (e, como alternativa, um servidor vCenter). 
**Failback** | Você precisará que um ambiente VMware execute failback do Azure, mesmo se estiver replicando servidores físicos. <br/><br/>Você precisará configurar um servidor de processo como uma VM do Azure <br/><br/>O servidor de configuração atuará como um servidor de destino mestre, mas se estiver executando failback de grandes volumes de tráfego, talvez você queira configurar um servidor de destino mestre local extra. [Saiba mais](site-recovery-failback-azure-to-vmware-classic.md)| O failback do site secundário para o site primário será destinado somente para VMware, mesmo se você fizer failover em um computador físico. Para o failback, você precisará configurar um servidor de destino mestre como uma VM no servidor primário vSphere.
**Conta do Azure** | Você precisará de uma conta e uma assinatura do [Azure](https://azure.microsoft.com/). | Não aplicável
**Armazenamento do Azure** | Você precisará de uma [conta de armazenamento do Azure](../storage/storage-redundancy.md#geo-redundant-storage) para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover. | Não aplicável
**Rede virtual do Azure** | Você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. Para executar o failback após o failover, você precisará de uma conexão de VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local. | Não aplicável
**Computadores protegidos** | Pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux. Durante a implantação, o serviço de mobilidade é instalado em cada computador que você deseja replicar. | Pelo menos uma máquina virtual de VMware ou servidor físico do Windows/Linux. Durante a implantação, o agente Unificado será instalado em cada computador que você deseja replicar.
**Conectividade** | Se o servidor de gerenciamento se conectar à Recuperação de Site por meio de um proxy, você precisará se certificar de que o servidor proxy possa se conectar com URLs específicas. | O servidor de configuração precisa ter acesso à Internet.
**Mais informações** | [Pré-requisitos de implantação detalhados](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | [Baixe](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o guia do usuário do InMage Scout.


## Requisitos de máquina virtual do Azure

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. Será necessário verificar se as máquinas virtuais locais que você deseja proteger atendem aos requisitos do Azure.


**Recurso** | **Requisitos** | **Detalhes**
---|---|---
Host Hyper-V | Deve estar executando o Windows Server 2012 R2 | A verificação de pré-requisitos falhará se o sistema operacional não tiver suporte
Hipervisor VMware | Sistema operacional com suporte | [Verifique os requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operacional convidado | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) do Windows e do Linux | A verificação de pré-requisitos falhará se não houver suporte.
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
- **Planejamento de capacidade**: leia sobre o [planejamento de capacidade](site-recovery-capacity-planner.md) para a Recuperação de Site.
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

<!---HONumber=AcomDC_0413_2016-->