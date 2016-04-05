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
	ms.date="03/27/2016"
	ms.author="raynew"/>


# Azure Site Recovery: perguntas frequentes

Leia este artigo para revisar as perguntas frequentes sobre o serviço Azure Site Recovery.

Se você ainda tiver dúvidas depois de ler este artigo, poste-as no final deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## Geral

### O que faz o Site Recovery?

O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de máquinas virtuais locais e de servidores físicos no Azure ou em um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### O que o Site Recovery pode replicar?

- **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V com suporte. 
- **Servidores físicos**: o Site Recovery pode replicar qualquer carga de trabalho em execução em um servidor físico com Windows/Linux.
- **Máquinas virtuais do VMware**: o Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM VMware com suporte.


### O que é necessário no Hyper-V?

Para o servidor de host do Hyper-V que você precisa depende do cenário de implantação. Verifique os pré-requisitos do Hyper-V em:

- [Replicando VMs Hyper-V no Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V em um datacenter secundário](site-recovery-vmm-to-vmm.md#before-you-start)

Para os convidados em execução no servidor de host do Hyper-V:

- Se você estiver replicando para um site secundário, leia sobre os [Sistemas operacionais convidados com suporte para as VMs do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se estiver replicando para o Azure, o Site Recovery dá suporte aos mesmos sistemas operacionais convidados que têm [suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Posso proteger as VMs se o Hyper-V estiver em execução em um sistema operacional cliente?

Não, as VMs devem estar localizadas em um servidor de host do Hyper-V sendo executado em uma máquina do servidor Windows com suporte. Se você precisar proteger um computador cliente, poderá replicá-lo como uma máquina física [para o Azure](site-recovery-vmware-to-azure-classic.md) ou um [datacenter secundário](site-recovery-vmware-to-vmware.md).


### Quais cargas de trabalho posso proteger com o Site Recovery?

O Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM ou servidor físico com suporte. O Site Recovery também fornece suporte para a replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra nos aplicativos da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.


### Preciso fazer com que os hosts Hyper-V estejam nas nuvens do System Center VMM para replicá-los com o Site Recovery? 

Se você quiser replicar em um datacenter secundário, as VMs Hyper-V deverão estar nos servidores host do Hyper-V em uma nuvem VMM. Se você quiser replicar para o Azure, então, poderá replicar as VMs nos servidores de host do Hyper-V com ou sem nuvem do VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).

### Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM? 

Sim. Você pode a replicar as VMs nos servidores do Hyper-V na nuvem do VMM para o Azure ou pode replicar entre as nuvens do VMM no mesmo servidor. Para a replicação local a local, recomendamos um servidor VMM nos sites primário e secundário. [Leia mais](site-recovery-single-vmm.md)

### Quais servidores físicos posso proteger?

Você pode proteger os servidores físicos em execução nos sistemas operacionais do Windows e do Linux com suporte para o Azure ou um site secundário. [Saiba](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos do sistema operacional para a replicação de site do Azure ou secundário. Observe que os servidores físicos replicados para o armazenamento do Azure serão executados como VMs no Azure quando ocorrer failover. Atualmente, quando você executa o failback do Azure para o site local, não há suporte para executar o failback para um servidor físico. Você só pode executar failback para uma máquina virtual em execução no VMware.

### Quais VMs VMware posso proteger?

Para proteger as VMs VMware, você precisará de um hipervisor vSphere e de máquinas virtuais executando as ferramentas VMware. Também recomendamos que você tenha um servidor VMware vCenter para gerenciar os hipervisores. [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos exatos para replicar os servidores VMware e VMs para o Azure ou um site secundário.

### Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?

Sim. Quando você usa o Site Recovery para coordenar a replicação e executar o failover em suas filiais, pode organizar e exibir todas as cargas de trabalho da filial em um local central. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.

## Segurança


### Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, a Recuperação de Site não intercepta dados replicados ou tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.

Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery é certificado pela ISO 27001:2005 e está em processo de conclusão de avaliação dos padrões HIPAA, DPA e FedRAMP JAB.

### Para fins de conformidade, até os metadados de nossos ambientes locais têm que permanecer na mesma região geográfica. A Recuperação de Site pode fazer isso?

Sim. Quando você cria um cofre do Site Recovery em uma região, garantimos que todos os metadados necessários para habilitar e coordenar a replicação e o failover permanecerão dentro dos limites geográficos da região.

### O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos replicando entre sites locais, a criptografia em trânsito tem suporte. Para máquinas virtuais e servidores físicos replicando no Azure, tanto a criptografia em trânsito quanto a criptografia em repouso (no Azure) têm suporte.



## Replicação

### Há algum pré-requisito para replicar máquinas virtuais no Azure?

As máquinas virtuais que você deseja replicar no Azure devem estar em conformidade com os [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?

Sim. A Recuperação de Site converte VMs da segunda geração para a primeira geração durante o failover. No failback, a VM é convertida novamente em segunda geração. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se eu replicar no Azure, como faço para pagar as VMs do Azure? 

Durante a replicação normal, os dados são replicados para o armazenamento do Azure com redundância geográfica e você não precisa pagar nada pela máquina virtual IaaS do Azure. Quando você executa um failover no Azure, o Site Recovery cria automaticamente máquinas virtuais IaaS do Azure e você será cobrado pelos recursos de computação que consumir no Azure.

### Há um SDK que posso usar para automatizar o fluxo de trabalho do Site Recovery?

Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Saiba mais sobre a [Implantação do Site Recovery usando o PowerShell e o Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

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

### Posso restringir a largura de banda alocada para o tráfego de replicação do Hyper-V?
- Se você estiver replicando entre VMs Hyper-V de dois sites locais, poderá usar o QoS do Windows. Eis um exemplo de script: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Se você estiver replicando máquinas virtuais Hyper-V no Azure, poderá configurar a limitação usando o seguinte cmdlet do PowerShell de exemplo:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [Saiba mais](https://support.microsoft.com/pt-BR/kb/3056159) sobre a limitação do tráfego do Hyper-V.
- [Saiba mais](site-recovery-vmware-to-azure-classic.md#capacity-planning) sobre a limitação da replicação do VMware para o Azure.


## Failover

### Como acessar máquinas virtuais do Azure após o failover para o Azure? 

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Rota Expressa do Azure. A comunicação em uma conexão VPN é enviada a portas internas na rede do Azure na qual a máquina virtual está localizada. As comunicações pela internet são mapeadas para os pontos de extremidade públicos no serviço de nuvem do Azure para VMs.

### Para o failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?

O Azure foi desenvolvido para resiliência. O Site Recovery já foi desenvolvido para fazer failover em um datacenter secundário do Azure de acordo com o SLA do Azure, se houver a necessidade. Se isso acontecer, fazemos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.

### Se eu estiver replicando entre dois sites, o que acontece se meu site primário sofrer uma interrupção inesperada?

Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### O failover é automático?

O failover não é automático. Você inicia os failovers com um único clique no portal ou pode usar os [cmdlets do PowerShell do Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx) para iniciar um failover. O failback é uma série de ações no portal do Site Recovery.

Para automatizar o failover, você pode usar o Orchestrator ou o Operations Manager para detectar a falha da máquina virtual e disparar o failover usando o SDK.


##
### Sou um provedor de serviço. O Site Recovery funciona com modelos de infraestrutura compartilhados ou dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### Para o provedor de serviço, a identidade do meu locatário é compartilhada com o serviço Site Recovery?
Não. Seus locatários não precisam acessar o portal do Site Recovery. Somente o administrador do provedor de serviços interage com o portal.


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

<!---HONumber=AcomDC_0330_2016-->