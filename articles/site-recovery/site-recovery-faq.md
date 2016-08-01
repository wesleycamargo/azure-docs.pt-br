<properties 
	pageTitle="Azure Site Recovery: perguntas frequentes | Microsoft Azure" 
	description="Este artigo aborda dúvidas comuns sobre o Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="get-started-article"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016" 
	ms.author="raynew"/>


# Azure Site Recovery: perguntas frequentes
## Sobre este artigo

Este artigo contém perguntas frequentes sobre o Azure Site Recovery. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Geral

###O que faz o Site Recovery?

O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de máquinas virtuais locais e de servidores físicos no Azure ou em um datacenter secundário. [Saiba mais](site-recovery-overview.md).


### O que o Site Recovery pode proteger?

- **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V.
- **Servidores físicos**: o Site Recovery pode proteger servidores físicos com o Windows ou o Linux em execução.
- **Máquinas virtuais VMware**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM VMware.

### O Recuperação de Site dá suporte ao modelo do Azure Resource Manager? 

Além da Recuperação de Site no Portal Clássico do Azure, a Recuperação de Site está disponível no Portal do Azure com suporte para o Resource Manager. Na maioria dos cenários de implantação, a Recuperação de Site no Portal do Azure fornece uma experiência de implantação simplificada e pode replicar VMs e servidores físicos no armazenamento clássico ou do Resource Manager. Aqui estão as implantações com suporte:

- [Replicar VMs VMware ou servidores físicos para o Azure no Portal do Azure](site-recovery-vmware-to-azure.md)
- [Replicar VMs Hyper-V em nuvens VMM para o Azure no Portal do Azure](site-recovery-vmm-to-azure.md)
- [Replicar VMs Hyper-V ( sem VMM) para o Azure no Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicar VMs Hyper-V em nuvens VMM para um site secundário no Portal do Azure](site-recovery-vmm-to-vmm.md)


### O que preciso no Hyper-V para orquestrar a replicação com a Recuperação de Site? 

Para o servidor de host do Hyper-V que você precisa depende do cenário de implantação. Verifique os pré-requisitos do Hyper-V em:

- [Replicando VMs Hyper-V no Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V em um datacenter secundário](site-recovery-vmm-to-vmm.md#before-you-start)

- Se você estiver replicando para um site secundário, leia sobre os [Sistemas operacionais convidados com suporte para as VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se estiver replicando no Azure, o Site Recovery dá suporte a todos os sistemas operacionais convidados [com suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?

Não, as VMs devem estar localizadas em um servidor de host do Hyper-V sendo executado em uma máquina do servidor Windows com suporte. Se você precisar proteger um computador cliente, poderá replicá-lo como um computador físico no [Azure](site-recovery-vmware-to-azure.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).


### Quais cargas de trabalho posso proteger com o Site Recovery?

Você pode usar a Recuperação de Site para proteger a maioria das cargas de trabalho em execução em uma VM ou em um servidor físico com suporte. O Site Recovery também fornece suporte para a replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra aos aplicativos da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.


### Hosts Hyper-V precisam estar em nuvens VMM? 

Se você quiser replicar para um datacenter secundário, as VMs Hyper-V deverão estar nos servidores host do Hyper-V em uma nuvem VMM. Se você quiser replicar para o Azure, poderá replicar as VMs nos servidores de host do Hyper-V com ou sem nuvens do VMM. [Leia mais](site-recovery-hyper-v-site-to-azure.md)

### Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM? 

Sim. Você pode a replicar as VMs nos servidores do Hyper-V na nuvem do VMM para o Azure ou pode replicar entre as nuvens do VMM no mesmo servidor. Para a replicação local a local, recomendamos um servidor VMM nos sites primário e secundário. [Leia mais](site-recovery-single-vmm.md)

### Quais servidores físicos posso proteger?

Você pode replicar servidores físicos que executem Windows e Linux para o Azure ou para um site secundário. [Saiba mais](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sobre os requisitos do sistema operacional. Os mesmos requisitos se aplicam independentemente de se estar replicando servidores físicos para o Azure ou para um site secundário.

Observe que servidores físicos serão executados como VMs no Azure se seu servidor local ficar inativo. Atualmente, não há suporte de failback para um servidor físico local, mas você pode executar failback para uma máquina virtual em execução no Hyper-V ou no VMware.


### Quais VMs VMware posso proteger?

Para proteger as VMs VMware, você precisará de um hipervisor vSphere e de máquinas virtuais executando as ferramentas VMware. Também recomendamos que você tenha um servidor VMware vCenter para gerenciar os hipervisores. [Saiba mais](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sobre os requisitos exatos para replicar os servidores VMware e VMs para o Azure ou um site secundário.

### Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?

Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.

## Segurança

### Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, a Recuperação de Site não intercepta dados replicados ou tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos. Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

A Recuperação de Site é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.


### Para fins de conformidade, até os metadados de nossos ambientes locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?

Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### O Site Recovery criptografa a replicação?

Para máquinas virtuais e servidores físicos que estão sendo replicados entre sites locais, há suporte para a criptografia em trânsito. Para máquinas virtuais e servidores físicos que estão sendo replicados no Azure, há suporte tanto para a criptografia em trânsito quanto para a criptografia em repouso (no Azure).


## Replicação


### Há algum pré-requisito para replicar máquinas virtuais no Azure?

As máquinas virtuais que você deseja replicar no Azure devem estar em conformidade com os [requisitos do Azure](site-recovery-best-practices.md#virtual-machines).

### Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?

Sim. O Site Recovery converte da segunda geração para a primeira geração durante o failover. No failback, a máquina é convertida novamente em segunda geração. [Leia mais](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se eu replicar no Azure, como faço para pagar as VMs do Azure? 

Durante a replicação normal, os dados são replicados no armazenamento do Azure com redundância geográfica e você não precisa pagar nada pela máquina virtual IaaS do Azure, o que é uma vantagem significativa. Quando você executa um failover no Azure, o Site Recovery cria automaticamente máquinas virtuais IaaS do Azure e você será cobrado pelos recursos de computação que consumir no Azure.


### Há um SDK que posso usar para automatizar o fluxo de trabalho do ASR?

Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar a Recuperação de Site usando o PowerShell:

- [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell clássico](site-recovery-deploy-with-powershell.md)
- [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell do Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Replicar VMs Hyper-V sem VMM para o Azure PowerShell clássico](site-recovery-hyper-v-site-to-azure-classic.md)
- [Replicar VMs Hyper-V sem VMM para o Azure PowerShell do Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)


### Se eu replicar no Azure, de que tipo de conta de armazenamento preciso?

- **Portal Clássico do Azure**: se você estiver implantando a Recuperação de Site no Portal Clássico do Azure, precisará de uma [conta de armazenamento com redundância geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage). Atualmente não há suporte para o armazenamento Premium. A conta deve estar na mesma região que o cofre da Recuperação de Site.

- **Portal do Azure**: se você estiver implantando a Recuperação de Site no Portal do Azure, precisará de uma conta de armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação. Haverá suporte para o Armazenamento Premium no momento apenas se você estiver replicando VMs VMware ou servidores físicos.

### Com que frequência posso replicar dados?

- **Hyper-v:** as VMs Hyper-V podem ser replicadas a cada 30 segundos, 5 minutos ou 15 minutos. Se você tiver definido a replicação da rede SAN, ela será simultânea.
- **VMware e servidores físicos:** não é relevante estabelecer uma frequência de replicação para eles. A replicação é contínua.

### Posso estender a replicação do site de recuperação existente para um site terciário?

Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### Posso fazer uma replicação offline na primeira vez em que replicar no Azure? 

Não há suporte para isso. Solicite esse recurso no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### Posso excluir discos específicos da replicação?

Há suporte para isso quando você estiver [replicando VMs VMware e servidores físicos](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) no Azure usando o Portal do Azure.


### Posso replicar máquinas virtuais com discos dinâmicos?

Os discos dinâmicos têm suporte na replicação de máquinas virtuais Hyper-V. Eles também terão suporte ao replicar VMs VMware e computadores físicos para o Azure se você estiver usando o [Portal do Azure](site-recovery-vmware-to-azure.md) ou o [Portal Clássico do Azure com implantação aprimorada](site-recovery-vmware-to-azure-classic.md). Observe que o disco do sistema operacional deve ser um disco básico.

### Posso restringir a largura de banda alocada para o tráfego de replicação do Hyper-V?

Sim. Você pode ler mais sobre a limitação de largura de banda nos artigos da implantação:

- [Planejamento de capacidade para a replicação de VMs VMware e servidores físicos](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Planejamento de capacidade para a replicação de VMs Hyper-V em nuvens VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Planejamento de capacidade para a replicação de VMs Hyper-V sem VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## Failover


### Se estou fazendo failover no Azure, como posso acessar as máquinas virtuais do Azure após o failover? 

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Rota Expressa do Azure. Você precisará preparar uma série de coisas para se conectar. Leia mais em:

- [Conectar VMs do Azure após o failover de VMs VMware ou servidores físicos](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Conectar VMs do Azure após o failover de VMs do Hyper-V em nuvens do VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Conectar VMs do Azure após o failover de VMs do Hyper-V sem VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?

O Azure foi desenvolvido para resiliência. O Site Recovery já foi desenvolvido para fazer failover em um datacenter secundário do Azure de acordo com o SLA do Azure, se houver a necessidade. Se isso acontecer, fazemos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.

### Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?

Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### O failover é automático?

O failover não é automático. Você pode iniciar failovers com um único clique no portal ou pode usar o [PowerShell do Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx) para disparar um failover. O failback também é uma ação simples no portal do Site Recovery.

Para automatizar, você pode usar o Orchestrator ou o Operations Manager para detectar a falha da máquina virtual e disparar o failover usando o SDK.

- [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
- [Saiba mais](site-recovery-failover.md) sobre failover.
- [Saiba mais](site-recovery-failback-azure-to-vmware.md) como realizar failback das VMs VMware e servidores físicos


## Provedores de serviço


### Sou um provedor de serviço. O Site Recovery funciona com modelos de infraestrutura compartilhados ou dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### Para o provedor de serviço, a identidade do meu locatário é compartilhada com o serviço da Recuperação de Site?
Não. A identidade do locatário permanece anônima. Seus locatários não precisam acessar o Portal da Recuperação de Site. Somente o administrador do provedor de serviços interage com o portal.


### Os dados de aplicativo dos meus locatários vão para o Azure?
Durante a replicação entre os sites de propriedade do provedor de serviços, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviço.

Se você estiver replicando no Azure, os dados de aplicativo são enviados para o armazenamento do Azure, mas não para o serviço de Recuperação de Site. Os dados são criptografados em trânsito e permanecem criptografados no Azure.


### Meus locatários receberão alguma fatura relativa a serviços do Azure?

Não. A relação de cobrança do Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### Ao replicar no Azure, precisamos executar máquinas virtuais no Azure o tempo todo?

Não. Os Dados são replicados para uma conta de armazenamento do Azure na sua assinatura. Quando você executa um failover de teste (simulação de recuperação de desastre), o Site Recovery cria máquinas virtuais automaticamente em sua assinatura.

### Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?

Sim.

### A quais plataformas vocês oferecem suporte atualmente?

Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e da Recuperação de Site.

### Você também dá suporte implantações únicas de Azure Pack e servidor VMM?

Sim, você pode replicar máquinas virtuais Hyper-V para o Azure, ou entre os sites de provedor de serviços. Observe que se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.


## Próximas etapas

- Leia a [visão geral do Site Recovery](site-recovery-overview.md)
- Saiba mais sobre a [arquitetura do Site Recovery](site-recovery-components.md)

 

<!---HONumber=AcomDC_0720_2016-->