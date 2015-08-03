<properties 
	pageTitle="Azure Site Recovery: perguntas frequentes" 
	description="Este artigo aborda dúvidas comuns sobre o uso do Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="06/02/2015" 
	ms.author="lauraa"/>


# Azure Site Recovery: perguntas frequentes
## Sobre este artigo

Este artigo contém perguntas frequentes sobre o Azure Site Recovery. Para ver uma introdução à Recuperação de Site e aos cenários de implantação relacionados, leia a [Visão geral da Recuperação de Site](site-recovery-overview.md).

Se você tiver outras dúvidas depois de ler este artigo, poste-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Geral
### A Recuperação Automatizada do Sistema é segura? Quais dados você envia para o Azure?

Sim, a Recuperação Automatizada do Sistema é segura. Ela não intercepta seus dados de aplicativo nem tem nenhuma informação sobre o que está em execução em suas máquinas virtuais. Não há necessidade de conectividade com a Internet, nem por parte dos hosts do Hyper-V, nem das máquinas virtuais.

Como a replicação ocorre entre seus próprios sites corporativos e de provedor de serviço, nenhum dado de aplicativo é enviado ao Azure. Somente metadados, como nomes de VM ou nuvem, que são necessários para administrar o failover, são enviados ao Azure. O ASR não tem a capacidade de interceptar dados de aplicativo, e esses dados sempre permanecem no local.

A Recuperação Automatizada do Sistema é certificada pela ISO 27001:2005 e está no processo de conclusão de avaliação dos padrões HIPAA, DPA e FedRAMP JAB.

### Os requisitos de conformidade exigem que até mesmo os metadados de nossos ambientes locais permaneçam na mesma região geográfica. A Recuperação Automatizada do Sistema pode garantir que possamos atender a esse requisito?

Sim. Quando você cria um Cofre de Recuperação de Site em uma região de sua escolha, garantimos que todos os metadados que precisamos para habilitar e administrar sua configuração de recuperação de desastre permaneçam dentro dos limites geográficos dessa região.

### Há um SDK que posso usar para automatizar o fluxo de trabalho do ASR?

Sim. Os fluxos de trabalho do ASR podem ser automatizados usando a API Rest, o PowerShell ou o SDK do Azure. Você pode encontrar mais detalhes na postagem de blog intitulada [Introdução ao suporte ao PowerShell no Azure Site Recovery](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/).

## Suporte de versão

### Quais versões de hosts e clusters do Windows Server têm suporte?
O Windows Server 2012 e o Windows Server 2012 R2 podem ser usados quando você escolhe a Réplica do Hyper-V para habilitar a replicação e a proteção entre sites do Hyper-V.


### Quais versões de sistemas operacionais convidados Hyper-V têm suporte?
A lista mais atual de sistemas operacionais convidados com suporte está disponível no tópico [Sobre máquinas virtuais e sistemas operacionais convidados](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Posso configurar a proteção da máquina virtual quando Hyper-V está em execução em um sistema operacional cliente?

Você não pode configurar o Hyper-V que está sendo executado em um sistema operacional do cliente para replicar máquinas virtuais no Azure. Somente há suporte para a Réplica do Hyper-V em sistemas operacionais do servidor. Você pode replicar os computadores cliente físicos no Azure usando [este artigo](site-recovery-vmware-to-azure.md).

### A Recuperação Automatizada do Sistema dá suporte à geração de 2 máquinas?

Atualmente, a Recuperação Automatizada do Sistema dá suporte à replicação de máquinas virtuais de geração 2 no Hyper-V para o Azure. A Recuperação Automatizada do Sistema se converte da geração 2 para a geração 1 durante o failover. Em failback, a máquina é convertida para a geração 1. [Leia mais](http://azure.microsoft.com/updates/azure-site-recovery-supports-gen-2-vm-protection-in-west-us-north-europe-and-japan-west/) sobre o suporte atual.


## Implantar entre os sites de provedores de serviço 

### Essa solução funciona para modelos de infraestrutura compartilhados ou dedicados?
Sim, o ASR oferece suporte aos modelos de infraestrutura dedicados e compartilhados.

### A identidade do meu locatário é compartilhada com o Azure?
Não. Na verdade, os locatários não precisam acessar o portal do ASR. Somente o administrador do provedor de serviços executa ações no portal ASR do Azure.

### Meus locatários receberão uma fatura do Azure?
Não. A relação de cobrança do Microsoft Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### Os dados de aplicativo dos meus locatários vão para o Azure?
Ao usar um site de propriedade do provedor de serviços para DR, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados e replicados diretamente entre os sites do provedor de serviço. Ao usar o Azure como um site de DR, os dados de aplicativo são enviados ao Azure – dados em repouso e em trânsito permanecem criptografados. Você também pode usar a VPN, a Rota Expressa ou a Internet Pública para estabelecer conectividade com o Azure.

### Ao usar o Azure como um site de DR, precisamos executar máquinas virtuais no Azure o tempo todo?
Não, o ASR foi projetado como uma solução de DRaaS de nuvem pública de primeira linha. Os dados são replicados em uma conta de armazenamento do Azure com redundância geográfica na sua própria assinatura. Quando você executa uma análise de DR ou um failover real, o ASR cria automaticamente as máquinas virtuais em sua assinatura.

### Vocês garantem o isolamento no nível de locatário quando opto por usar o Azure como um site de DR?
Sim.

### A quais plataformas vocês oferecem suporte atualmente?
Oferecemos suporte ao Azure Pack, Sistema de Plataforma de Nuvem, Hyper 2012 baseado no System Center e a implantações superiores. Para saber mais sobre a integração do ASR e do Azure Pack, consulte [Configurar proteção de máquinas virtuais](https://technet.microsoft.com/library/dn850370.aspx).

### Você também dá suporte ao pacote único do Azure e a implantações de servidor único do VMM?
Sim, as implantações de um único SCVMM têm suporte para os cenários de DR entre sites de provedores de serviços e DR entre um provedor de serviços e o Azure.

A implantação de um único Azure Pack também tem suporte quando você habilita a DR entre os sites de provedores de serviços, no entanto, a integração do Runbook do ASR não está disponível para essa topologia.

## Implantar entre sites do Hyper-V e do Azure (sem o VMM)

### O ASR exige que você habilite a VPN site a site?
Não, isso não é obrigatório. O ASR funciona por Internet pública também. No entanto, se a VPN site a site estiver configurada, você poderá acessar as máquinas virtuais com failover da mesma maneira que fazia antes. Para obter mais informações sobre considerações de rede ao habilitar a Recuperação de Desastre no Azure, consulte o [blog sobre Configuração de infraestrutura de rede para o Microsoft Azure como um site de Recuperação de Desastre](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/).

### Preciso pagar algum valor a mais sobre a cobrança de Proteção do ASR?
No estado estável, replicamos alterações no Armazenamento do Azure com Redundância Geográfica e você não precisa pagar nada pela máquina virtual de IaaS do Azure (uma vantagem significativa). Quando você executa um failover no Azure, o ASR cria automaticamente máquinas virtuais de IaaS do Azure, e somente depois você é cobrado pelos recursos de computação que consome no Azure.

### Tenho uma filial na qual não tenho o VMM instalado. É possível habilitar a proteção das cargas de trabalho para o Azure na filial? Quais são os principais benefícios de usar o ASR para fazer isso?
Sim, você pode usar o ASR para proteger máquinas virtuais em execução nos hosts Hyper-V que não são gerenciados pelo SCVMM.

Ao usar o ASR para gerenciar as necessidades de recuperação de desastre de todas as suas filiais, você obtém uma exibição unificada de todas as cargas de trabalho das filiais em um local central. Você também obtém failover por um clique e administração de recuperação de desastre para todas as filiais do escritório principal, sem nunca precisar ir até a filial. Atualmente, o mapeamento e a criptografia em repouso da rede não têm suporte para recuperação de desastre em filiais.

### Para as filiais, há um limite no número de máquinas virtuais que posso proteger?
Não. O suporte é igual ao do nosso cenário corporativo.

### É necessário instalar um agente nas máquinas virtuais que deseja proteger?

Você não precisa de um agente em máquinas virtuais. Você precisa instalar o Provedor de Recuperação de Site e o Agente de Serviços de Recuperação em cada servidor Hyper-V executando máquinas virtuais que deseja proteger. Esses dois componentes são instalados com um único arquivo de Provedor que pode ser baixado durante a implantação da Recuperação de Site.

### O provedor em execução no servidor Hyper-V precisa de alguma configuração de firewall especial?

Nenhuma configuração específica é necessária. Os componentes do Provedor no servidor se comunicam por uma conexão HTTPS para o Azure e usam a configuração padrão da Internet no servidor de host Hyper-V, a menos que seja especificado de outra forma.

### O servidor Hyper-V precisa ser um membro do domínio?

Não, o servidor não precisa estar em um domínio

### Quais versões de hosts e clusters do Windows Server têm suporte?
O Windows Server 2012 e o Windows Server 2012 R2 podem ser usados ao usar a Recuperação Automatizada do Sistema e a replicação Hyper-V entre sites Hyper-V e o Azure.

### Quais versões de sistemas operacionais convidados têm suporte?
A lista mais atual de sistemas operacionais convidados com suporte está disponível no artigo [Sobre máquinas virtuais e sistemas operacionais convidados](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

## Implantar entre dois data centers VMM

### Posso replicar uma máquina virtual que foi replicada para um site secundário no Azure? 

Não, esse tipo de replicação encadeada não tem suporte


## Implantar entre dois data centers VMM com SAN

### E se eu já tiver uma replicação baseada em SAN configurada e não quiser mudá-la?
Sem problemas. O ASR oferece suporte ao cenário em que a replicação já possa estar configurada, caso em que você pode aproveitar o ASR para administração da recuperação de desastre centrada na máquina virtual, configuração da rede de recuperação de desastre, planos de recuperação com reconhecimento de aplicativo, práticas de recuperação de desastre e seus requisitos de relatório de auditoria e conformidade.


### Preciso do VMM? Preciso do VMM em ambos os lados?
Sim. Precisamos que a matriz SAN seja colocada sob gerenciamento do VMM usando um provedor SMI-S específico da matriz.

Damos suporte a implantações únicas de HA do VMM com base no tipo de matriz, embora a configuração recomendada seja usar servidores VMM separados para gerenciar os sites.


### E se eu não tiver certeza sobre meu administrador de armazenamento?
Trabalhamos com replicação existente configurada pelo seu administrador de armazenamento, o que significa que o administrador de armazenamento não precisa fazer alterações em suas matrizes. No entanto, as organizações que desejam automatizar o seu gerenciamento de armazenamento por meio do SCVMM também podem provisionar armazenamento usando a Recuperação Automatizada do Sistema e o VMM.

### Posso oferecer suporte à replicação síncrona? Clusters convidados? Armazenamento compartilhado?
Sim, sim e sim.

### O que eu preciso instalar no local para que isso funcione?
Ao usar a replicação baseada em matriz (SAN) para habilitar a replicação e a proteção entre sites do Hyper-V, tudo o que você precisa é que o provedor de DR do ASR seja instalado nos servidores SCVMM. Lembre-se também de que esse é o *único* host que precisa de conectividade com a Internet.


Suas matrizes também devem ser detectadas pelo SCVMM usando um provedor SMI-S atualizado que é disponibilizado pelos respectivos fornecedores de armazenamento.

## Implantar entre servidores físicos e o Azure

### Posso proteger o meu servidor físico local no Azure?

Sim, você pode replicar um servidor físico usando a Recuperação Automatizada do Sistema e executá-lo como uma VM no Azure caso o servidor local fique inativo. Atualmente, não há suporte para o failback para um servidor físico local. Você pode executar failback para uma máquina virtual em execução no Hyper-V ou no VMware.


## Failover

### E se o Azure for desativado? Posso disparar um failover do VMM?

Embora o Azure tenha sido desenvolvido para resiliência de serviço, gostamos de nos preparar o pior. O ASR já foi desenvolvido para fazer failover em um datacenter secundário do Azure, de acordo com o SLA do Azure. Também garantimos que até mesmo quando isso acontece, seus metadados e o Cofre do ASR permaneçam na mesma região geográfica que você escolheu para seu cofre.


### E se o ISP do meu datacenter primário também sofrer uma interrupção durante um desastre? E se o ISP do meu datacenter secundário também sofrer uma interrupção?

Você pode usar o portal do ASR para executar um failover não planejado com um único clique. Não é necessário ter uma conectividade no datacenter primário para executar o failover.

É provável que os aplicativos que precisam estar em execução no seu datacenter secundário após um failover precisarão de alguma forma de conectividade com a Internet. O ASR pressupõe que, mesmo que o site primário e o ISP possam ser afetados durante um desastre, o servidor SCVMM do site secundário ainda estará conectado ao ASR.

### O ASR oferece suporte ao failback?
Sim. Fazer failback do Azure de volta no site local é simples usando o processo de um clique no portal do ASR.

### O failover automático é possível?

Não há nenhuma opção de failover automático. Você também pode pressionar um único botão no portal para disparar um failover, ou ainda pode usar [PowerShell de Recuperação de Site](https://msdn.microsoft.com/library/dn850420.aspx) para disparar um failover. Use o Orchestrator ou o Operations Manager local para testemunhar a falha da máquina virtual e, em seguida, decidir disparar o failover usando o SDK. Isso o torna um processo automático.




## Monitoramento

### Quais soluções vocês fornecem para monitorar minha configuração e replicação contínua?
Você pode se inscrever para receber alertas por email diretamente do serviço ASR, a fim de ser notificado sobre quaisquer problemas que precisem de atenção. Também é possível monitorar a integridade da replicação contínua usando o SCOM.

## Próximas etapas

Para iniciar a implantação do ASR:

- [Configurar a proteção entre um site VMM local e o Azure](site-recovery-vmm-to-azure.md) 
- [Configurar a proteção entre um site do Hyper-V local e o Azure](site-recovery-hyper-v-site-to-azure.md) 
- [Configurar a proteção entre dois sites VMM locais](site-recovery-vmm-to-vmm.md) 
- [Configurar a proteção entre dois sites VMM locais com SAN](site-recovery-vmm-san.md) 
- [Configurar a proteção com um único servidor VMM](site-recovery-single-vmm.md) 

 

<!---HONumber=July15_HO4-->