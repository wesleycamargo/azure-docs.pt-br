<properties
	pageTitle="Práticas recomendadas para implantação da Recuperação de Site"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores locais no Azure ou em um armazenamento de dados secundário."
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
	ms.date="05/08/2015"
	ms.author="raynew"/>

# Práticas recomendadas para implantação da Recuperação de Site


<a id="overview" name="overview" href="#overview"></a>

## Sobre este artigo

Artigo que inclui as práticas recomendadas e que você deve ler e implementar antes da implantação da Recuperação de Site do Azure. Se você estiver procurando por uma introdução à Recuperação de Site e por cenários de implantação relacionados, leia a [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).

Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Preparando o Azure

- **Conta do Azure**: você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Se você não tiver uma, comece com uma [avaliação gratuita](pricing/free-trial/).
- Leia sobre [preços](pricing/details/site-recovery/) de Recuperação de Site. 
- **Armazenamento do Azure**: se você implantar a Recuperação de Site com replicação para o Azure, precisará de uma conta de armazenamento do Azure. Você pode configurar essa conta durante a implantação ou preparar uma antes de iniciar. A conta deve ter a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. Leia [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md). 

## Máquinas virtuais

Se você desejar replicar para o armazenamento do Azure, observe o seguinte:

- **Sistema operacional com suporte**: você pode implantar a Recuperação de Site a fim de orquestrar a proteção de máquinas virtuais executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux.
- **Suporte a VHDX**: apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converte automaticamente o VHDX em VHD ao realizar o failover no Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
- **Requisitos do Azure**: verifique se as máquinas virtuais que você deseja proteger atendem aos requisitos do Azure.

**Recurso da máquina virtual** | **Suporte** | **Detalhes**
---|---|---
Sistema operacional host | Windows Server 2012 R2 | A verificação de pré-requisitos falhará se não houver suporte
Sistema operacional convidado | <p>Windows Server 2008 R2 ou posterior</p><p>Linux: Centos, openSUSE, SUSE, Ubuntu</p> | A verificação de pré-requisitos falhará se não houver suporte. Atualize o valor no console VMM.
Arquitetura do sistema operacional convidado | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do disco do sistema operacional | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Contagem do disco do sistema operacional | 1 | A verificação de pré-requisitos falhará se não houver suporte. Atualize o valor no console VMM
Contagem de disco de dados | 16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) | A verificação de pré-requisitos falhará se não houver suporte
Tamanho do VHD do disco de dados | Até 1023 GB | A verificação de pré-requisitos falhará se não houver suporte
Adaptadores de rede | Há suporte para vários adaptadores |
Endereço IP estático | Suportado | Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure
Disco iSCSI | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
VHD compartilhado | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Disco FC | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
Formato de disco rígido| <p>VHD</p><p>VHDX</p> |
Nome da máquina virtual| Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número | Atualize o valor nas propriedades da máquina virtual na Recuperação de Site
Tipo de máquina virtual | <p>Geração 1</p> <p>Geração 2 - Windows</p> | Há suporte para a máquina virtual de 2ª geração com o tipo de disco do sistema operacional de um disco básico que inclui os volumes de dados 1 ou 2 com o formato de disco como VHDX, que é menor do que 300GB. Não há suporte para as máquinas virtuais Linux Geração 2. [Para obter mais informações](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## Servidores VMM

A Recuperação de Site pode orquestrar a replicação de máquinas virtuais localizadas em servidores host Hyper-V em nuvens do System Center Virtual Machine Manager (VMM) da seguinte maneira: - replicar de um servidor VMM local para o Azure (usando a Réplica do Hyper-V) - replicar para um site secundário local (usando a Réplica do Hyper-V). Recomendamos a implantação com um servidor VMM no site primário e um no site secundário. No entanto, se precisar, você poderá [implantar um único servidor VMM](site-recovery-single-vmm.md) para os dois sites. - replicar para um site secundário local (usando SAN). Você precisará de um armazenamento de dados primário e secundário com um servidor VMM em cada site. Se você quiser implantar o VMM com a Recuperação de Site, será necessário configurar sua infraestrutura do VMM. Se você não tiver um servidor VMM, leia mais sobre isso clicando [aqui](site-recovery-hyper-v-site-to-azure.md).


### Verificar a versão do VMM

**Versão do VMM com suporte** | **Cenário de Recuperação de Site com suporte** | **Host do Hyper-V com suporte (origem/destino)** | **Provedor/agente com suporte**
---|---|---|---
VMM no System Center 2012 R2 pelo menos o Pacote Cumulativo de Atualizações 5 (somente virtual no cluster) | Proteção local para local com a replicação de SAN | Pelo menos o Windows Server 2012 com as atualizações mais recentes | Versão mais recente do Provedor do Azure Site Recovery (instalar em servidores VMM) |
VMM no System Center 2012 R2 (recomendado) (cluster ou autônomo) | <p>Proteção local para local com a replicação do Hyper-V</p> <p>Proteção local para o Azure com a replicação do Hyper-V</p> | <p>Pelo menos o Windows Server 2012 com as atualizações mais recentes</p><p>Windows Server 2012 R2 (origem, não aplicável para o destino)</p> | <p>Versão mais recente do Provedor do Azure Site Recovery (instalar em servidores VMM)</p> <p>Versão mais recente do Agente de Serviços de Recuperação do Azure (instalar em servidores host Hyper-V apenas para replicação no Azure)</p>
VMM no System Center 2012 SP1 com a atualização cumulativa mais recente (cluster ou autônoma) | Local para proteção local | Windows Server 2012 com as atualizações mais recentes | Versão mais recente do Provedor do Azure Site Recovery (instalar em servidores VMM) |

### Configurar a infraestrutura de nuvem do VMM

Qualquer cenário de VMM que você queira implantar com a Recuperação de Site exige a criação de pelo menos duas nuvens privadas (uma no servidor VMM de origem e outra no destino) no VMM para obter as vantagens do modelo de nuvem privada durante a definição, gerenciamento e acesso da nuvem e de seus recursos subjacentes usando o console do VMM. Uma nuvem do VMM coleta e apresenta um conjunto agregado de recursos, cujo uso é limitado pelas configurações de capacidade em nuvem e pelas cotas de função de usuário. Usuários de autoatendimento podem gerenciar e usar recursos em nuvem sem entender totalmente sua infraestrutura subjacente. Os recursos podem ser adicionados com facilidade a fim de reduzir ou aumentar a resiliência em nuvem. Se você precisar configurar nuvens, use os recursos abaixo:


- [Novidades na nuvem privada com o System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 e as nuvens](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
- [Configurando a malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [Criando uma nuvem privada no VMM](https://technet.microsoft.com/pt-br/library/jj860425.aspx)
- [Passo a passo: criando nuvens privadas com o System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).




## Provedores e agentes

Provedores e agentes são instalados em servidores locais para que possam se conectar à Recuperação de Site no Azure. Eles se conectam pela Internet usando uma conexão HTTPS criptografada.

- **Requisitos do provedor**: durante a implantação, você instalará alguns componentes em seus servidores locais:
	- **Em servidores VMM**: instale o Provedor do Azure Site Recovery nos servidores VMM que você deseja registrar no cofre.
	- **Em servidores host Hyper-V localizados em uma nuvem do VMM**: instale o agente de Serviços de Recuperação do Azure.
	- **Em servidores host Hyper-V sem um servidor VMM**: instale o Provedor do Azure Site Recovery e o Agente de Serviços de Recuperação do Azure em cada servidor do host Hyper-V ou no nó do cluster.
-  **Instalação do provedor**: ao instalar o provedor e os agentes perceba o seguinte:
	-  Você deve instalar a mesma versão do Provedor em todos os servidores em um cofre de Recuperação de Site. Não é possível usar versões diferentes dentro de um único cofre.
	-  Se você quiser replicar para o Azure a partir dos servidores Hyper-V em uma nuvem do VMM, o servidor VMM deverá estar executando o System Center 2012 R2. Se você quiser replicar para um armazenamento de dados secundário, o VMM deve estar executando o System Center 2012 com SP1 ou R2.
- **Proxy**: não é necessário adicionar exceções de firewall ou criar um proxy específico. Se você quiser usar um proxy personalizado para a conexão com o Provedor, você precisará fazer o seguinte antes de começar a implantação:

	- Configurar o servidor proxy personalizado antes de instalar o Provedor.
	- Permitir essas URLs através do firewall:
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- Se você estiver implantando a Recuperação de Site com o VMM e usar um proxy personalizado, uma conta de RunAs VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas nas configurações personalizadas de proxy no portal de Recuperação de Site. Será necessário configurar o servidor proxy para que essa conta possa ser autenticada.



## Conectividade local

- **Conectividade do provedor**: provedores e agentes são instalados em servidores locais para que possam se conectar à Recuperação de Site. Eles se conectam à Recuperação de Site pela Internet usando uma conexão HTTPS criptografada. Não é necessário adicionar exceções de firewall ou criar um proxy específico.
- **Conectividade com a Internet**: você precisará de conectividade com a Internet do servidor da seguinte maneira:
	- Se você estiver protegendo máquinas virtuais em servidores host Hyper-V em uma nuvem do VMM, apenas o servidor VMM precisará de uma conexão com a Internet.
	- Se você estiver protegendo máquinas virtuais em servidores host Hyper-V sem um servidor VMM, apenas os servidores host Hyper-V precisarão de uma conexão com a Internet.
	- Você não precisa de conectividade com a Internet nas máquinas virtuais que quer proteger.
- **VPN site a site**: não é necessário uma conexão VPN site a site para se conectar à Recuperação de Site. No entanto, se você tiver uma conexão site a site poderá acessar máquinas virtuais em estado de failover, assim como fazia antes do failover. Observe que ao replicar para o Azure você configurará uma rede VPN site a site entre o site local e uma rede específica do Azure. Ela não é usada para o tráfego de replicação criptografado. Esse tráfego ocorre pela internet até a conta de armazenamento do Azure em sua assinatura.
- **Conectividade após o failover**: para garantir que os usuários possam se conectar a máquinas virtuais após o failover para o Azure, faça o seguinte:
	- Habilite o RDP para as máquinas virtuais antes do failover.
	- Após o failover, use uma conexão site a site para que você possa se conectar a elas como fazia anteriormente, ou habilite o ponto de extremidade RDP da máquina.

## Armazenamento

- **Conta de armazenamento do Azure**: se você estiver replicando para o Azure, precisará de uma conta de armazenamento do Azure. A conta deve ter a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. Para saber mais, leia [Introdução ao armazenamento do Microsoft Azure](../storage/storage-introduction.md).
- **Mapeamento de armazenamento**: se você estiver replicando máquinas virtuais em servidores VMM locais, poderá configurar o mapeamento de armazenamento para certificar-se de que as máquinas virtuais estão conectadas da melhor maneira possível ao armazenamento após o failover. Quando você replica entre dois sites VMM locais, a réplica da máquina virtual é armazenada, por padrão, no local indicado no servidor host Hyper-V de destino. É possível configurar o mapeamento entre classificações de armazenamento VMM em servidores VMM de origem e de destino. Se você quiser usar esse recurso, defina as classificações de armazenamento antes de começar a implantação. [Leia mais](site-recovery-storage-mapping.md) sobre mapeamento de armazenamento.
- **SAN**: se você quiser replicar entre dois sites locais com a replicação SAN, perceba o seguinte:
	- Você só pode replicar máquinas virtuais Hyper-V em um datacenter secundário usando a replicação SAN. Não é possível replicar para o Azure.
	- Você pode usar seu ambiente atual de SAN. Não é necessário fazer qualquer alteração em matrizes de SAN.
	- Você precisará verificar se a matriz de SAN recebe [suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
	- Você precisará de dois servidores VMM nos sites de origem e de destino para a implantação de SAN.
	- Verifique se os clusters do host Hyper-V estão executando o Windows Server 2012 ou 2012 R2 se você quiser implantar a replicação SAN. [Leia sobre](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) sistemas operacionais convidados compatíveis com versões diferentes do Hyper-V.
	- Você precisará descobrir e classificar o armazenamento de SAN no VMM,
	- Se você ainda não estiver replicando, após a detecção você precisará criar LUNs e alocar o armazenamento no console VMM. Se você já estiver replicando, ignore esta etapa.
	- As instruções completas estão incluídas neste [artigo](site-recovery-vmm-san.md).


## Redes

- **Saiba mais sobre considerações de rede**: [Leia mais](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) sobre considerações de rede e as práticas recomendadas.

- **Configurar o mapeamento de rede**: o mapeamento de rede é um elemento importante da implantação do VMM e da Recuperação de Site. Ele coloca da melhor maneira possível as máquinas virtuais replicadas nos servidores host Hyper-V de destino e garante que suas máquinas virtuais replicadas estejam conectadas às redes apropriadas após o failover. Você pode configurar o mapeamento de rede durante a replicação para o Azure ou para um armazenamento de dados secundário:
	- **Mapeamento de rede para o Azure**: se você estiver replicando para o Azure, o mapeamento de rede garantirá que todas as máquinas que sofrerem failover na mesma rede poderão conectar-se umas às outras, independentemente do plano de recuperação a que cada uma delas pertence. Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais. Se você não configurar o mapeamento de rede, apenas as máquinas que sofrerem failover no mesmo plano de recuperação poderão se conectar.
	- **Mapeamento de rede para o site secundário**: se você estiver replicando para site secundário do VMM, o mapeamento de rede garantirá que as máquinas virtuais estejam conectadas às redes apropriadas após o failover, e que as máquinas virtuais de réplica serão colocadas da melhor forma possível em servidores host Hyper-V. Se você não configurar o mapeamento de rede, as máquinas de réplica não serão conectadas a qualquer rede de VM.
	- [Leia mais](site-recovery-network-mapping) sobre o mapeamento de rede.
- **Configurar redes de VMM**:
	- Configurar corretamente as redes lógicas e de VM no VMM. Leia sobre [redes lógicas](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) e [redes de VM](https://technet.microsoft.com/library/jj721575.aspx).
	- Certifique-se de que todas as máquinas virtuais no servidor VMM de origem estejam conectadas a uma rede de VM.
	- Verifique se essas redes de VM estão vinculadas a uma rede lógica associada à nuvem.
	- Se você estiver replicando para o Azure, crie redes virtuais no Azure. Observe que várias redes VM podem ser mapeadas para uma única rede do Azure. Leia [Tarefas de configuração da rede virtual](https://msdn.microsoft.com/library/azure/dn133795.aspx).

## Otimizando o desempenho

- **Tamanho do volume do sistema operacional**: quando você replicar uma máquina virtual para o Azure, o volume do sistema operacional deverá ter menos de 127 GB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
- **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e realizar de forma efetiva o failover de uma máquina virtual de aproximadamente 32 TB.
- **Limites de plano de recuperação**: a Recuperação de Site pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
- **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços em nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
- **Planejamento de capacidade**: para obter orientação, use o [Planejador de capacidade para Réplica do Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=39057).
- **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
	- **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
	- **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
	- **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta. Além disso, se você estiver replicando para a Recuperação de Site do Azure usando o agente de Backup do Azure. Você pode configurar a limitação para esse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: se você quiser medir o objetivo do tempo de recuperação (RTO) que você pode esperar com Recuperação de Site, sugerimos a execução de um teste de failover e a exibição dos trabalhos de Recuperação de Site a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
- **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.

## Realizando o failover
- **Interrupção primária**: se você estiver replicando de um armazenamento de dados local para outro armazenamento, e ambos os armazenamentos enfrentarem uma interrupção no site primário, execute um failover não planejado a partir do portal de Recuperação de Site. Não é necessário ter uma conectividade do armazenamento de dados primário para executar o failover.
- **Reter o endereço IP após o failover para o site secundário**: se você deseja manter o endereço IP de uma máquina virtual de origem após o failover para um armazenamento de dados secundário, execute as etapas descritas [aqui](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx).
- **Reter endereço IP após o failover para o Azure**: você pode especificar o IP que será concedido à VM em estado de failover na guia Configurar da máquina virtual. Para obter detalhes, consulte como [configurar as propriedades de rede da máquina virtual](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines)
- **Reter o endereço IP público**: se você quiser manter um endereço IP público após o failover para um site secundário, a Recuperação de Site não impedirá se seu ISP oferecer suporte a isso. Não é possível manter um endereço IP público depois de fazer failover no Azure.
- **Reter endereços internos não RFC no Azure**: é possível reter espaços de endereço não RFC 1918 após o failover para o Azure.
- **Failover parcial para o armazenamento de dados secundário**: se você realizar um failover de um site parcial para o armazenamento de dados secundário e quiser se conectar novamente ao site primário, poderá usar VPN site a site para conectar um aplicativo em estado de failover no site secundário com componentes de infraestrutura em execução no site primário. Observe que se toda a sub-rede falhar, você poderá manter o endereço IP da máquina virtual. Ao fazer failover de uma sub-rede parcial, você não pode manter o endereço IP da máquina virtual porque as sub-redes não podem ser divididas entre sites.
- **Failover parcial para o Azure**: se você realizar um failover de um site parcial para o Azure e quiser se conectar novamente ao site primário, poderá usar VPN site a site para conectar um aplicativo em estado de failover no Azure com componentes de infraestrutura em execução no site primário. Observe que se toda a sub-rede falhar, você poderá manter o endereço IP da máquina virtual. Ao fazer failover de uma sub-rede parcial, você não pode manter o endereço IP da máquina virtual porque as sub-redes não podem ser divididas entre sites.
- **Reter letra da unidade**: se você quiser manter a letra da unidade em máquinas virtuais após o failover, defina a política SAN para a máquina virtual como **Ativado**. [Leia mais](https://support.microsoft.com/kb/3031135).
- **Encaminhando solicitações de clientes após o failover para o Azure**: a Recuperação de Site funciona com o Gerenciador de Tráfego do Azure para encaminhar solicitações de cliente ao seu aplicativo após o failover. Você pode usar scripts nos planos de recuperação (com a automação do Azure) para executar atualizações de DNS.

## Integração

- **Integração com outras tecnologias BCDR**: a Recuperação de Site integra-se a outras tecnologias de recuperação de desastre e continuidade dos negócios (BCDR). Para a replicação com base em aplicativo, recomendamos o uso do SQL Server AlwaysOn para replicar máquinas virtuais executando o banco de dados e a Réplica do Hyper-V para máquinas virtuais front-end. O AlwaysOn exige licenças do SQL Server Enterprise nos sites primário e secundário e uma máquina virtual do Azure em execução em sua assinatura. O uso da replicação integrada da Recuperação de Site é útil quando há a probabilidade de os aplicativos enfrentarem algum tempo de inatividade e, nesse caso, recomendamos a Recuperação de Site para orquestrar a replicação do banco de dados, de aplicativos e máquinas virtuais front-end. Essa abordagem economiza dinheiro com a versão do SQL Server exigida, com o número de licenças e com o custo de manutenção de máquinas virtuais em execução no Azure.

## Próximas etapas

Depois de revisar essas práticas recomendadas, inicie a implantação da Recuperação de Site:

- [Configurar a proteção entre um site VMM local e o Azure](site-recovery-vmm-to-azure.md)
- [Configurar a proteção entre um site do Hyper-V local e o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configurar a proteção entre dois sites VMM locais](site-recovery-vmm-to-vmm.md)
- [Configurar a proteção entre dois sites VMM locais com SAN](site-recovery-vmm-san.md)
- [Configurar a proteção com um único servidor VMM](site-recovery-single-vmm.md)
 

<!---HONumber=July15_HO2-->