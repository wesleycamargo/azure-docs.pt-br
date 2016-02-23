<properties
	pageTitle="Recuperação de Site: perguntas frequentes | Microsoft Azure"
	description="Este artigo responde a dúvidas comuns sobre o Azure Site Recovery."
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
## Sobre este artigo

Este artigo contém perguntas frequentes sobre o Site Recovery. Se você tiver dúvidas após a leitura destas perguntas frequentes, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Suporte

###O que faz o Site Recovery?

O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de máquinas virtuais locais e de servidores físicos no Azure ou em um datacenter secundário. [Saiba mais](site-recovery-overview.md).


### O que o Site Recovery pode proteger?

- **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V.
- **Servidores físicos**: o Site Recovery pode proteger servidores físicos com o Windows ou o Linux em execução.
- **Máquinas virtuais VMware**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM VMware.


###Quais VMs Hyper-V posso proteger?

Depende do cenário de implantação.

Verifique os pré-requisitos de servidor host do Hyper-V:

- [Replicando VMs Hyper-V no Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicando VMs Hyper-V em um datacenter secundário](site-recovery-vmm-to-vmm.md#before-you-start)

Em relação ao sistemas operacionais convidados:

- Se você estiver replicando em um datacenter secundário, veja uma lista dos sistemas operacionais convidados com suporte de VMs em execução nos servidores host do Hyper-V em [Sistemas operacionais convidados com suporte de VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se estiver replicando no Azure, o Site Recovery dá suporte a todos os sistemas operacionais convidados [com suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

O Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM com suporte.

### Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?

Não, não há suporte. Como solução alternativa, você precisará replicar o computador como um computador físico [para o Azure](site-recovery-vmware-to-azure-classic.md) ou um [datacenter secundário](site-recovery-vmware-to-vmware.md).


### Quais cargas de trabalho posso proteger com o Site Recovery?

Você pode usar o Site Recovery para proteger a maioria das cargas de trabalho em execução em uma máquina virtual ou em um servidor físico. O Site Recovery pode ajudá-lo a implantar a recuperação de desastre com reconhecimento de aplicativo. Ele se integra a aplicativos da Microsoft, incluindo o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. Você pode personalizar sua solução de recuperação de desastre para cada aplicativo específico. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.


### Preciso ter sempre um servidor VMM do System Center para proteger máquinas virtuais Hyper-V?

Não. Além de poder replicar VMs Hyper-V localizadas em nuvens VMM, você também pode replicar VMs Hyper-V em um ambiente que não tenha o VMM implantado. [Saiba mais](site-recovery-hyper-v-site-to-azure.md). Observe que, se você quiser replicar para um datacenter secundário, servidores host Hyper-V deverão ser gerenciados em nuvens VMM.

### Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM?

Sim. Você pode a replicar máquinas virtuais Hyper-V em nuvem no servidor VMM no Azure ou pode replicar entre nuvens VMM no mesmo servidor. Recomendamos que, para replicação local a local, você tenha um servidor VMM nos sites primário e secundário. [Leia mais](site-recovery-single-vmm.md)

### Quais servidores físicos posso proteger?

Você pode proteger servidores físicos que executem Windows e Linux no Azure ou em site secundário. [Saiba](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) mais sobre os requisitos de sistema operacional. As mesmas limitações se aplicam independentemente de se estar replicando servidores físicos para o Azure ou para um site secundário.

Observe que servidores físicos serão executados como VMs no Azure se seu servidor local ficar inativo. Atualmente, não há suporte para o failback para um servidor físico local. Você precisará realizar o failback para uma VM VMware.

### Quais VMs VMware posso proteger?

Para esse cenário, você precisará de um servidor vCenter VMware, de um hipervisor vSphere e de máquinas virtuais executando ferramentas VMware. [Saiba](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) os requisitos exatos. As mesmas limitações se aplicam independentemente de se estar replicando servidores físicos para o Azure ou para um site secundário.

### Há algum pré-requisito para replicar máquinas virtuais no Azure?

As máquinas virtuais que você deseja replicar no Azure devem estar em conformidade com os [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?

Sim. O Site Recovery converte da segunda geração para a primeira geração durante o failover. No failback, a máquina é convertida novamente em segunda geração. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se eu replicar no Azure, como faço para pagar as VMs do Azure?

Durante a replicação normal, os dados são replicados no armazenamento do Azure com redundância geográfica e você não precisa pagar nada pela máquina virtual IaaS do Azure, o que é uma vantagem significativa. Quando você executa um failover no Azure, o Site Recovery cria automaticamente máquinas virtuais IaaS do Azure e você será cobrado pelos recursos de computação que consumir no Azure.

### Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?

Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.

### Há um SDK que posso usar para automatizar o fluxo de trabalho do Site Recovery?

Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Saiba mais sobre a [implantação da Recuperação de Site usando o PowerShell e o Gerenciador de Recursos do Azure](site-recovery-deploy-with-powershell-resource-manager.md).


## Segurança e conformidade

### Os meus dados são enviados para o serviço de Recuperação de Site?

Não, o Site Recovery não intercepta dados de aplicativo ou tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.

Os dados de replicação são trocados entre hosts Hyper-V, hipervisores VMware ou servidores físicos nos data centers primário e secundário, ou entre o datacenter e o armazenamento do Azure. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery é certificado pela ISO 27001:2005 e está em processo de conclusão de avaliação dos padrões HIPAA, DPA e FedRAMP JAB.

### Para fins de conformidade, até os metadados de nossos ambientes locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?

Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### O Site Recovery criptografa a replicação?
Quando a replicação de máquinas virtuais e servidores físicos é feita entre sites locais, a criptografia em trânsito tem suporte. Ao replicar máquinas virtuais e servidores físicos entre sites locais e o Azure, tanto a criptografia em trânsito quanto a criptografia no Azure têm suporte.




## Replicação e failover

### Se eu replicar no Azure, de que tipo de conta de armazenamento preciso?

Você precisará de uma conta de armazenamento com [armazenamento com redundância geográfica padrão](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Atualmente não há suporte para o armazenamento Premium.

### Com que frequência posso replicar dados?
- **Hyper-v:** VMs Hyper-V no Windows Server 2012 R2 podem ser replicadas a cada 30 segundos, cinco minutos ou 15 minutos. Se você tiver definido a replicação SAN, ela será simultânea.
- **VMware e servidores físicos:** não é relevante estabelecer uma frequência de replicação para eles. A replicação será contínua.

### Posso estender a replicação do site de recuperação existente para um site terciário?
Esse tipo de replicação estendida ou encadeada não tem suporte. Envie comentários sobre esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/).


### Posso fazer uma replicação offline na primeira vez em que replicar no Azure?

Não há suporte para isso. Envie-nos comentários sobre esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/).


### Posso excluir discos específicos da replicação?

Não há suporte para isso. Envie-nos comentários sobre esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/).

### Posso replicar máquinas virtuais com discos dinâmicos?

Os discos dinâmicos têm suporte na replicação de máquinas virtuais Hyper-V. Eles não têm suporte na replicação de máquinas virtuais VMware ou de servidores físicos. Envie-nos comentários sobre esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/).

### Se estou fazendo failover no Azure, como posso acessar as máquinas virtuais do Azure após o failover?

Você pode acessar as VMs do Azure em uma conexão segura de Internet ou em uma VPN site a site (ou na Rota Expressa do Azure) se tiver uma. A comunicação em uma conexão VPN é enviada a portas internas na rede do Azure na qual a máquina virtual está localizada. As comunicações pela internet são mapeadas para os pontos de extremidade públicos no serviço de nuvem do Azure para máquinas virtuais.

### Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?

O Azure foi desenvolvido para resiliência de serviço. O Site Recovery já foi desenvolvido para fazer failover em um datacenter secundário do Azure de acordo com o SLA do Azure, se houver a necessidade. Se isso acontecer, fazemos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.

### Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?

Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.


### O failover é automático?

O failover não é automático. Você inicia os failovers com um único clique no portal ou pode usar o [cmdlets do PowerShell da Recuperação de Site](https://msdn.microsoft.com/library/dn850420.aspx) para disparar um failover. O failback também é uma ação simples no portal do Site Recovery. Para automatizar, você pode usar o Orchestrator ou o Operations Manager para detectar a falha da máquina virtual e disparar o failover usando o SDK.

### Se eu estou replicando VMs Hyper-V, posso restringir a largura de banda alocada para o tráfego de replicação do Hyper-V?
- Se você estiver replicando entre VMs Hyper-V de dois sites locais, poderá usar o QoS do Windows. Eis um exemplo de script:

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Se você estiver replicando máquinas virtuais Hyper-V no Azure, poderá configurar a limitação usando o seguinte cmdlet do PowerShell de exemplo:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## Implantação do provedor de serviço

### O Site Recovery funciona com modelos de infraestrutura compartilhados ou dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### A identidade do meu locatário é compartilhada com o serviço de Recuperação de Site?
Não. Na verdade, os locatários não precisam acessar o portal do Site Recovery. Somente o administrador do provedor de serviços executa ações no portal.


### Os dados de aplicativo dos meus locatários vão para o Azure?
Durante a replicação entre os sites de propriedade do provedor de serviços, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviço.

Se você estiver replicando no Azure, os dados de aplicativo são enviados para o armazenamento do Azure, mas não para o serviço de Recuperação de Site. Os dados são criptografados em trânsito e permanecem criptografados no Azure.

### Meus locatários receberão alguma fatura relativa a serviços do Azure?

Não. A relação de cobrança do Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### Ao replicar no Azure, precisamos executar máquinas virtuais no Azure o tempo todo?

Não, os Dados são replicados em uma conta de armazenamento do Azure com redundância geográfica na sua própria assinatura. Quando você executa um failover de teste (simulação de recuperação de desastre), o Site Recovery cria máquinas virtuais automaticamente em sua assinatura.

### Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?

Sim.

### A quais plataformas vocês oferecem suporte atualmente?

Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). Saiba mais sobre a integração do Azure Pack e Site Recovery em [Configurar proteção de máquinas virtuais](https://technet.microsoft.com/library/dn850370.aspx).

### Você também dá suporte implantações únicas de Azure Pack e servidor VMM?

Sim, você pode replicar máquinas virtuais Hyper-V e o Azure, ou entre os sites de provedor de serviços. Observe que se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.


## Próximas etapas

- Leia a [visão geral do Site Recovery](site-recovery-overview.md)
- Saiba mais sobre a [arquitetura do Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0218_2016-->