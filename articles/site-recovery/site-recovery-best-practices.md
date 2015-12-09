<properties
	pageTitle="Preparar-se para a implantação do Azure Site Recovery"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos localizados em servidores locais para o Azure ou para um datacenter secundário."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

# Preparar-se para a implantação do Azure Site Recovery

## Sobre este artigo

Este artigo descreve como se preparar para a implantação do Azure Site Recovery. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Protegendo as máquinas virtuais Hyper-V

Você tem algumas opções de implantação para proteger máquinas virtuais Hyper-V. É possível replicar VMs Hyper-V locais no Azure ou em um datacenter secundário. Há requisitos diferentes para cada implantação.

**Requisito** | **Replicar no Azure (com VMM)** | **Replicar VMs Hyper-V no Azure (sem VMM)** | **Replicar VMs Hyper-V no site secundário (com VMM)** | **Detalhes**
---|---|---|---|---
**VMM** | VMM em execução no System Center 2012 R2 <br/><br/>Pelo menos uma nuvem do VMM que contém um ou mais grupos de hosts do VMM. | ND | Servidores VMM nos sites primário e secundário em execução pelo menos no System Center 2012 SP1 com as atualizações mais recentes. <br/><br/> Pelo menos uma nuvem em cada servidor VMM. As nuvens devem ter o perfil de capacidade do Hyper-V definido.<br/><br/> A nuvem de origem deve ter pelo menos um grupo de hosts do VMM. | Opcional. Não é necessário ter o System Center VMM implantado para replicar máquinas virtuais Hyper-V no Azure, mas, se você o fizer, precisará se certificar de que o servidor VMM esteja configurado adequadamente. Isso inclui verificar se você está executando a versão correta do VMM e se as nuvens estão configuradas.
**Hyper-V** | Pelo menos um servidor de host Hyper-V no datacenter local executando o Windows Server 2012 R2 ou posterior | Pelo menos um servidor Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012, com as atualizações mais recentes instaladas e conectado à Internet.<br/><br/> Os servidores Hyper-V devem estar em um grupo de hosts em uma nuvem VMM. | Pelo menos um servidor Hyper-V nos sites de origem e de destino executando pelo menos o Windows Server 2012, com as atualizações mais recentes instaladas e conectado à Internet.<br/><br/> Os servidores Hyper-V devem estar localizados em um grupo de hosts em uma nuvem VMM. | 
**Máquinas virtuais** | Pelo menos uma VM no servidor de host Hyper-V de origem | Pelo menos uma VM no servidor de host Hyper-V na nuvem VMM de origem | Pelo menos uma VM no servidor de host Hyper-V na nuvem VMM de origem. | As VMs que estão replicando no Azure devem atender aos [pré-requisitos de máquina virtual do Azure](site-recovery-best-practices/#virtual-machines)
**Conta do Azure** | Você precisará de uma conta do [Azure](http://azure.microsoft.com/) e de uma assinatura do serviço Recuperação de Site. | Você precisará de uma conta do [Azure](http://azure.microsoft.com/) e de uma assinatura do serviço Recuperação de Site. | ND | Se você não tiver uma conta, comece com uma [avaliação gratuita](pricing/free-trial/). Leia sobre os [preços](pricing/details/site-recovery/) do serviço.
**Armazenamento do Azure** | Você precisará de uma assinatura para uma conta de armazenamento do Azure com a replicação geográfica habilitada. | Você precisará de uma assinatura para uma conta de armazenamento do Azure com a replicação geográfica habilitada. | ND | A conta deve estar na mesma região que o cofre do Azure Site Recovery e estar associada à mesma assinatura. [Saiba mais sobre armazenamento](../storage/storage-introduction.md).
**Mapeamento de armazenamento** | ND | ND | Como opção, você pode definir o mapeamento do armazenamento para certificar-se de que as máquinas virtuais sejam conectadas da melhor forma possível ao armazenamento após o failover. Quando você replica entre dois sites VMM locais, a réplica da máquina virtual é armazenada, por padrão, no local indicado no servidor host Hyper-V de destino. É possível configurar o mapeamento entre classificações de armazenamento VMM em servidores VMM de origem e de destino. | Para usar esse recurso, será necessário definir as classificações de armazenamento antes de começar a implantação. [Leia mais](site-recovery-storage-mapping.md).
**Replicação SAN** | ND | ND | Se você quiser replicar entre dois sites VMM locais usando a replicação SAN, poderá usar o ambiente SAN existente. | Você precisará de uma [matriz SAN com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) e o armazenamento SAN deve ser descoberto e classificado na VMM.<br/><br/>Se você não estiver replicando no momento, será necessário criar LUNs e alocar o armazenamento no console VMM. Se você já estiver replicando, ignore esta etapa.
**Rede** | Configure o mapeamento de rede para assegurar que todas as máquinas que passarem por failover na mesma rede do Azure possam se conectar entre si, independentemente do plano de recuperação a que cada uma delas pertença. Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais. Se você não configurar o mapeamento de rede, apenas as máquinas que sofrerem failover no mesmo plano de recuperação poderão se conectar. | ND | <br/><br/>Configure o mapeamento de rede para assegurar que as máquinas virtuais estejam conectadas às redes apropriadas após o failover e que as máquinas virtuais de réplica sejam colocadas da forma ideal em servidores de host Hyper-V. Se você não configurar o mapeamento de rede, as máquinas replicadas não serão conectadas a qualquer rede de VM após o failover. | [Leia mais](site-recovery-network-mapping) sobre o mapeamento de rede. <br/><br/> Para configurar o mapeamento de rede com o VMM, será necessário garantir que as redes lógicas do VMM e as redes de VM estejam configuradas corretamente. [Saiba mais](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) e [Redes de VM](https://technet.microsoft.com/library/jj721575.aspx)). Além disso, leia sobre as [considerações de rede para o VMM](site-recovery-network-design/#vmm-design).  
**Provedores e agentes** | Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM. Em servidores do host Hyper-V localizados em nuvens do VMM, você instalará o agente dos Serviços de Recuperação do Azure. | Durante a implantação, você instalará o provedor do Azure Site Recovery e o agente de Serviços de Recuperação do Azure no servidor ou cluster do host Hyper-V.| Durante a implantação, você instalará o provedor do Azure Site Recovery nos servidores do VMM. Em servidores do host Hyper-V localizados em nuvens do VMM, você instalará o agente dos Serviços de Recuperação do Azure. | Os provedores e agentes se conectam à Recuperação de Site pela Internet usando uma conexão HTTPS criptografada. Não é necessário adicionar exceções de firewall ou criar um proxy específico para a conexão do Provedor, mas se você quiser usar um proxy personalizado, permita essas URLs através do firewall antes de começar a implantação: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net **Conectividade com a Internet** | Somente os servidores do VMM precisam de uma conexão com a Internet | Somente os servidores de host Hyper-V precisam de uma conexão com a Internet | Somente servidores do VMM precisam de uma conexão com a Internet | Máquinas virtuais não precisam de nada instalado e não se conectam diretamente à Internet.



## Proteger máquinas virtuais VMware ou servidores físicos.

Há algumas opções de implantação para a proteção de máquinas virtuais VMware ou de servidores físicos com Windows/Linux. É possível replicá-las no Azure ou em um datacenter secundário. Há requisitos diferentes para cada implantação.

**Requisito** | **Replicar VMs VMware/servidores físicos para o Azure** | **Replicar VMs VMware/servidores físicos para um site secundário**  
---|---|--- 
**Site primário** | **Servidor de processo**: um servidor dedicado com Windows (físico ou virtual) | **Servidor de processo**: um servidor dedicado com Windows (físico ou máquina virtual VMware)<br/><br/>  
**Site local secundário** | ND | **Servidor de configuração**: um servidor dedicado com Windows (físico ou virtual) <br/><br/> **Servidor de destino mestre**: um servidor dedicado (físico ou virtual). Configure usando o Windows para proteger máquinas com Windows, ou usando o Linux para proteger máquinas com Linux.
**As tabelas** | **Assinatura**: você precisará de uma assinatura para o serviço Recuperação de Site. Leia sobre [preços](pricing/details/site-recovery/) <br/><br/> **Conta de armazenamento**: você precisará de uma conta de armazenamento com replicação geográfica habilitada. A conta deve estar na mesma região que o cofre do Site Recovery e estar associada à mesma assinatura. [Leia mais](../storage/storage-introduction.md)<br/><br/> **Servidor de configuração**: você precisará configurar o servidor de configuração como uma VM do Azure <br/><br/> **Servidor de destino mestre**: você precisará configurar o servidor de destino mestre como uma VM do Azure <br/><br/> Configure usando o Windows para proteger máquinas com Windows, ou usando o Linux para proteger máquinas com Linux.<br/><br/> **Rede virtual do Azure**: você precisará de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implantados. Ela deve estar na mesma assinatura e na mesma região que o cofre do Azure Site Recovery | ND  
**Máquinas virtuais/servidores físicos** | Pelo menos uma máquina virtual VMware ou servidor físico com Windows/Linux.<br/><br/>Durante a implantação, o serviço de Mobilidade será instalado em cada máquina| Pelo menos uma VM VMware ou servidor físico com Windows/Linux.<br/><br/> Durante a implantação, o Agente Unificado será instalado em cada máquina.




## Requisitos de máquina virtual do Azure

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. Será necessário verificar se as máquinas virtuais locais que você deseja proteger atendem aos requisitos do Azure.


**Recurso** | **Suporte** | **Detalhes**
---|---|---
Sistema operacional host Hyper-V | Windows Server 2012 R2 | A verificação de pré-requisitos falhará se não houver suporte
Sistema de operacional do hipervisor VMware | Executando um sistema operacional com suporte | [Detalhes](site-recovery-vmware-to-azure/#before-you-start) 
Sistema operacional convidado | Para replicação do Hyper-V para o Azure, a Recuperação de Site dá suporte a todos os sistemas operacionais [com suporte pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico, verifique os [pré-requisitos](site-recovery-vmware-to-azure/#before-you-start) do Windows e Linux | A verificação de pré-requisitos falhará se não houver suporte. 
Arquitetura do sistema operacional convidado | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do disco do sistema operacional | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Contagem do disco do sistema operacional | 1 | A verificação de pré-requisitos falhará se não houver suporte. 
Contagem de disco de dados | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do VHD do disco de dados | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Adaptadores de rede | Há suporte para vários adaptadores |
Endereço IP estático | Suportado | Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure
Disco iSCSI | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
VHD compartilhado | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Disco FC | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Formato de disco rígido| VHD <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
Nome da máquina virtual| Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
Tipo de máquina virtual | <p>Geração 1</p> <p>Geração 2 - Windows</p> | Há suporte para a máquina virtual de 2ª geração com o tipo de disco do sistema operacional de um disco básico que inclui os volumes de dados 1 ou 2 com o formato de disco como VHDX, que é menor do que 300GB. Não há suporte para as máquinas virtuais Linux Geração 2. [Para obter mais informações](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Otimizando sua implantação

Use as dicas a seguir para ajudá-lo a otimizar e dimensionar sua implantação.

- **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e realizar de forma efetiva o failover de uma máquina virtual de aproximadamente 32 TB.
- **Limites de plano de recuperação**: a Recuperação de Site pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços em nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: leia [planejador de capacidade](http://www.microsoft.com/en-in/download/details.aspx?id=39057) se você estiver replicando VMs do Hyper-V.
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
	- **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
	- **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
	- **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta. Além disso, se você estiver replicando para a Azure Site Recovery usando o agente de Backup do Azure. Você pode configurar a limitação para esse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: se você quiser medir o objetivo do tempo de recuperação (RTO) que você pode esperar com Recuperação de Site, sugerimos a execução de um teste de failover e a exibição dos trabalhos de Recuperação de Site a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.





## Próximas etapas

Depois de revisar essas práticas recomendadas, inicie a implantação da Recuperação de Site:

- [Configurar a proteção entre um site VMM local e o Azure](site-recovery-vmm-to-azure.md)
- [Configurar a proteção entre um site do Hyper-V local e o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configurar a proteção entre dois sites VMM locais](site-recovery-vmm-to-vmm.md)
- [Configurar a proteção entre dois sites VMM locais com SAN](site-recovery-vmm-san.md)
- [Configurar a proteção com um único servidor VMM](site-recovery-single-vmm.md)
 

<!---HONumber=AcomDC_1203_2015-->