<properties 
	pageTitle="Recuperação de Site: perguntas frequentes | Microsoft Azure" 
	description="Este artigo responde a perguntas comuns sobre o Azure Site Recovery."
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Azure Site Recovery: perguntas frequentes

Este artigo contém perguntas frequentes sobre o Site Recovery.

Se você ainda tiver dúvidas depois de ler este artigo, poste-as no final deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Visão geral

A Recuperação de Site contribui com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) ajudando a garantir que as cargas de trabalho e aplicativos permaneçam disponíveis após as interrupções. A Recuperação de Site orquestra e gerencia a replicação de máquinas virtuais locais e de servidores físicos para o Azure, ou para um datacenter secundário. [Saiba mais](site-recovery-overview.md).


### O que o Site Recovery pode proteger?

- **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V. 
- **Servidores físicos**: a Recuperação de Site pode proteger as cargas de trabalho em execução em servidores físicos com Windows/Linux.
- **Máquinas virtuais VMware**: a Recuperação de Site pode proteger qualquer carga de trabalho em execução em uma VM VMware.


### Quais são os requisitos do Hyper-V?

Os requisitos para o servidor host do Hyper-V dependem do cenário de implantação. Verifique os pré-requisitos em:

- [Replicando VMs Hyper-V no Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V em um datacenter secundário](site-recovery-vmm-to-vmm.md#before-you-start)

Requisitos para os convidados em execução no servidor host do Hyper-V:

- Se você estiver replicando em um local secundário, leia sobre os sistemas operacionais convidados com suporte para VMs em [Sistemas operacionais convidados com suporte para VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx) em execução nos servidores host do Hyper-V.
- Se estiver replicando no Azure, o Site Recovery dá suporte a todos os sistemas operacionais convidados [com suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?

Não, as VMs devem estar localizadas em um servidor host do Hyper-V executando uma máquina com Windows Server com suporte. Se você precisar proteger um computador cliente, poderá replicá-lo como uma máquina física [no Azure](site-recovery-vmware-to-azure-classic.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).


### Quais cargas de trabalho posso proteger com o Site Recovery?

A Recuperação de Site pode replicar a maioria das cargas de trabalho em execução em um servidor físico ou VM com suporte. A Recuperação de Site também fornece suporte para replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra a aplicativos da Microsoft, incluindo o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.


### Preciso ter sempre um servidor VMM do System Center para proteger máquinas virtuais Hyper-V? 

Se você quiser replicar em um datacenter secundário, as VMs Hyper-V deverão estar nos servidores host do Hyper-V em uma nuvem VMM. Porém, se quiser replicar no Azure, você poderá replicar as VMs em servidores host do Hyper-V com ou sem nuvens VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).

### Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM? 

Sim. Você pode a replicar máquinas virtuais Hyper-V em nuvem no servidor VMM no Azure ou pode replicar entre nuvens VMM no mesmo servidor. Para replicação local a local nós recomendamos um servidor VMM nos sites primário e secundário. [Leia mais](site-recovery-single-vmm.md)

### Quais servidores físicos posso proteger?

Você pode proteger servidores físicos que executem Windows e Linux no Azure ou em site secundário. [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos do sistema operacional. As mesmas limitações se aplicam independentemente de se estar replicando servidores físicos para o Azure ou para um site secundário.

Observe que servidores físicos serão executados como VMs no Azure se seu servidor local ficar inativo. Atualmente, não há suporte para o failback para um servidor físico local. Você só pode executar failback para uma máquina virtual em execução no VMware.

### Quais VMs VMware posso proteger?

Para proteger as VMs VMware, você precisará de um hipervisor vSphere e de máquinas virtuais executando as ferramentas VMware. Também recomendamos que você tenha um servidor VMware vCenter para gerenciar os hipervisores. [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos exatos para VMs e servidores VMware. As mesmas limitações se aplicam independentemente da replicação dos servidores físicos ser no Azure ou em um site secundário.

### Há algum pré-requisito para replicar máquinas virtuais no Azure?

As máquinas virtuais que você deseja replicar no Azure devem estar em conformidade com os [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?

Sim. A Recuperação de Site converte VMs da segunda geração para a primeira geração durante o failover. No failback, a VM é convertida novamente em segunda geração. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se eu replicar no Azure, como faço para pagar as VMs do Azure? 

Durante a replicação normal, os dados são replicados no armazenamento do Azure com redundância geográfica e você não precisa pagar nada pela máquina virtual IaaS do Azure, o que é uma vantagem significativa. Quando você executa um failover no Azure, o Site Recovery cria automaticamente máquinas virtuais IaaS do Azure e você será cobrado pelos recursos de computação que consumir no Azure.

### Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?

Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.

### Há um SDK que posso usar para automatizar o fluxo de trabalho do Site Recovery?

Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Saiba mais sobre a [implantação da Recuperação de Site usando o PowerShell e o Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).


## Segurança e conformidade

### Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, a Recuperação de Site não intercepta dados replicados ou tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.

Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery é certificado pela ISO 27001:2005 e está em processo de conclusão de avaliação dos padrões HIPAA, DPA e FedRAMP JAB.

### Para fins de conformidade, até os metadados de nossos ambientes locais têm que permanecer na mesma região geográfica. A Recuperação de Site pode fazer isso?

Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos replicando entre sites locais, a criptografia em trânsito tem suporte. Para máquinas virtuais e servidores físicos replicando no Azure, tanto a criptografia em trânsito quanto a criptografia em repouso (no Azure) têm suporte.




## Replicação e failover

### Se eu replicar no Azure, de que tipo de conta de armazenamento preciso?

Você precisará de uma conta de armazenamento com [armazenamento com redundância geográfica padrão](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Atualmente não há suporte para o armazenamento Premium.

### Com que frequência posso replicar dados?
- **Hyper-v:** VMs Hyper-V no Windows Server 2012 R2 podem ser replicadas a cada 30 segundos, cinco minutos ou 15 minutos. Se você tiver definido a replicação SAN, ela será simultânea.
- **VMware e servidores físicos:** não é relevante estabelecer uma frequência de replicação para eles. A replicação será contínua. 

### Posso estender a replicação do site de recuperação existente para outro site de recuperação?
Esse tipo de replicação estendida ou encadeada não tem suporte. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) sobre esse recurso.


### Posso fazer uma replicação offline na primeira vez em que replicar no Azure? 

Não há suporte para isso. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) sobre esse recurso.


### Posso excluir discos específicos da replicação?

Não há suporte para isso. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) sobre esse recurso.

### Posso replicar máquinas virtuais com discos dinâmicos?

Os discos dinâmicos têm suporte na replicação de máquinas virtuais Hyper-V. Eles também têm suporte ao replicar VMs VMware e máquinas físicas, se você estiver usando a [implantação aprimorada](site-recovery-vmware-to-azure-classic.md). Observe que o disco do sistema operacional deve ser um disco básico.


### Como acessar máquinas virtuais do Azure após o failover para o Azure? 

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Rota Expressa do Azure. A comunicação em uma conexão VPN é enviada a portas internas na rede do Azure na qual a máquina virtual está localizada. As comunicações pela internet são mapeadas para os pontos de extremidade públicos no serviço de nuvem do Azure para VMs.

### Para o failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?

O Azure foi desenvolvido para resiliência. O Site Recovery já foi desenvolvido para fazer failover em um datacenter secundário do Azure de acordo com o SLA do Azure, se houver a necessidade. Se isso acontecer, fazemos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.

### Se eu estiver replicando entre dois sites, o que acontece se meu site primário sofrer uma interrupção inesperada?

Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.


### O failover é automático?

O failover não é automático. Você inicia os failovers com um único clique no portal ou pode usar o [cmdlets do PowerShell da Recuperação de Site](https://msdn.microsoft.com/library/dn850420.aspx) para disparar um failover. Failback é uma série de ações no portal da Recuperação de Site.

Para automatizar o failover, você pode usar o Orchestrator ou o Operations Manager para detectar a falha da máquina virtual e disparar o failover usando o SDK.

### Posso restringir a largura de banda alocada para o tráfego de replicação do Hyper-V?
- Se você estiver replicando entre VMs Hyper-V de dois sites locais, poderá usar o QoS do Windows. Eis um exemplo de script: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Se você estiver replicando máquinas virtuais Hyper-V no Azure, poderá configurar a limitação usando o seguinte cmdlet do PowerShell de exemplo:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [Saiba mais](https://support.microsoft.com/pt-BR/kb/3056159) sobre a limitação do tráfego do Hyper-V.
- [Saiba mais](site-recovery-vmware-to-azure-classic.md#capacity-planning) sobre a limitação da replicação do VMware no Azure.



## Implantação do provedor de serviço

### O Site Recovery funciona com modelos de infraestrutura compartilhados ou dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### A identidade do meu locatário é compartilhada com o serviço de Recuperação de Site?
Não. Na verdade, os locatários não precisam acessar o portal da Recuperação de Site. Somente o administrador do provedor de serviços interage com o portal.


### Os dados de aplicativo dos meus locatários vão para o Azure?
Durante a replicação entre os sites de propriedade do provedor de serviços, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviço.

Se você estiver replicando no Azure, os dados de aplicativo são enviados para o armazenamento do Azure, mas não para o serviço de Recuperação de Site. Os dados são criptografados em trânsito e permanecem criptografados no Azure.

### Meus locatários receberão alguma fatura relativa a serviços do Azure?

Não. A relação de cobrança do Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### Ao replicar no Azure, precisamos executar máquinas virtuais no Azure o tempo todo?

Não. Os Dados são replicados em uma conta de armazenamento do Azure com redundância geográfica na sua própria assinatura. Quando você executa um failover de teste (simulação de recuperação de desastre), o Site Recovery cria máquinas virtuais automaticamente em sua assinatura.

### Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?

Sim.

### A quais plataformas vocês oferecem suporte atualmente?

Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações do System Center (2012 e posterior). Saiba mais sobre a integração do Azure Pack em [Configurar proteção para máquinas virtuais](https://technet.microsoft.com/library/dn850370.aspx).

### Você também dá suporte implantações únicas de Azure Pack e servidor VMM?

Sim, você pode replicar máquinas virtuais Hyper-V e o Azure, ou entre os sites de provedor de serviços. Observe que se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.


## Próximas etapas

- Leia a [visão geral do Site Recovery](site-recovery-overview.md)
- Saiba mais sobre a [arquitetura do Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0316_2016-->