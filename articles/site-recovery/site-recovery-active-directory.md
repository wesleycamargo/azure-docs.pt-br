<properties
	pageTitle="Orientação sobre o ASR para o Active Directory | Microsoft Azure" 
	description="Este artigo explica detalhadamente como você pode criar uma solução de recuperação de desastres para seu AD usando o Azure Site Recovery, executar failovers planejados/não planejados/de teste usando um plano de recuperação de um clique, configurações com suporte e pré-requisitos." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#Solução automatizada de DR para Active Directory e DNS usando ASR


Todos os aplicativos corporativos, como o SharePoint, o Dynamics AX e o SAP dependem da infraestrutura de AD e DNS para funcionarem corretamente. Durante a criação de uma solução de recuperação de desastres (DR) para qualquer aplicativo desse tipo, é importante proteger e recuperar o AD antes que os outros componentes do aplicativo sofram uma interrupção.

O Azure Site Recovery é um serviço baseado no Azure que fornece recursos de recuperação de desastres por meio da orquestração de replicação, failover e recuperação de máquinas virtuais. O Azure Site Recovery oferece suporte a várias tecnologias de replicação a fim de replicar, proteger e executar de forma consistente o failover de máquinas virtuais e aplicativos em nuvens privadas/públicas ou do hoster.

Com o Azure Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para seu AD. Você poderá iniciar o failover em segundos e de qualquer lugar no caso de uma interrupção e fazer o AD funcionar em alguns minutos. Caso você tenha um AD para vários aplicativos, como o SharePoint e SAP em seu site primário, e decidir aplicar o failover a todo o site, aplique o failover primeiro no AD usando o ASR e depois aplique o failover nos outros aplicativos usando planos de recuperação específicos ao aplicativo.

Este artigo explica detalhadamente como você pode criar uma solução de recuperação de desastres para seu AD, executar failovers planejados/não planejados/de teste usando um plano de recuperação de um clique, configurações com suporte e pré-requisitos. O público-alvo deve estar familiarizado com o AD e o Azure Site Recovery. Há duas opções recomendadas com base na complexidade do ambiente local do cliente para proteger o AD.

####Opção 1

Se houver uma quantidade pequena de aplicativos e um único controlador de domínio no ambiente e você for executar o failover de todo o site, recomendamos o uso da replicação do ASR a fim de replicar a máquina controladora de domínio em um site secundário (aplicável para Site a Site e Site para o Azure). A mesma máquina virtual replicada também pode ser usada para o Failover de Teste.

####Opção 2
Se houver muitos aplicativos e houver mais de um controlador de domínio no ambiente, ou se você planeja executar o failover de alguns aplicativos por vez, recomendamos que além de habilitar a replicação do ASR na máquina virtual do controlador de domínio (que será usada para o Failover de Teste), você também configure um controlador de domínio adicional no site de DR (site local secundário ou no Azure).

>[AZURE.NOTE]Mesmo se você escolher a Opção-2 para realizar um failover de teste, ainda será necessário configurar a replicação do ASR para o controlador de domínio. Leia a seção [considerações sobre o failover de teste](#considerations-for-test-failover) para saber mais.


As seções a seguir explicam como habilitar a proteção no controlador de domínio no ASR e como configurar um controlador de domínio no Azure.


##Pré-requisitos

A implementação da recuperação de desastres para o AD usando o Azure Site Recovery exige os seguintes pré-requisitos.

- Uma implantação local do servidor DNS e do Active Directory
- O cofre dos serviços do Azure Site Recovery foi criado na assinatura do Microsoft Azure 
- Se o Azure for seu site de recuperação, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs, a fim de garantir que sejam compatíveis com as VMs do Azure e com os serviços do Azure Site Recovery


##Habilitar a proteção para o Controlador de domínio/DNS usando o ASR


###Proteger a VM
Habilite a proteção da VM do Controlador de Domínio/DNS no ASR. Execute a configuração relevante do Azure Site Recovery com base na condição de a VM estar implantada no Hyper-V ou no VMware. A frequência consistente de Falha recomendada é de 15 minutos.

###Definir as configurações de rede da VM
- Para a máquina virtual do Controlador de Domínio/DNS, defina as configurações de rede no ASR de modo que a VM seja anexada à rede correta após um failover. 
- Por exemplo, ao usar o Azure como o site do DR, você pode selecionar a VM na "Nuvem VMM" ou o "Grupo de Proteção" para definir as configurações de rede, conforme mostra o instantâneo abaixo.
- 
![Configurações de rede da VM](./media/site-recovery-active-directory/VM-Network-Settings.png)

##Habilitar a proteção do AD usando a replicação do AD
###Cenário de Site a Site

Crie um controlador de domínio no site secundário (DR) e, durante a promoção do servidor à função de controlador de domínio, dê o nome do mesmo domínio que está sendo usado no site primário. É possível usar o snap-in Sites e Serviços do Active Directory para definir as configurações no objeto de link de site ao qual os sites foram adicionados. Ao definir as configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e com que frequência. Consulte [‘Agendar a replicação entre sites’](https://technet.microsoft.com/pt-BR/library/cc731862.aspx) para obter mais detalhes.

###Cenário de Site para o Azure
Siga estas instruções para criar um [controlador de domínio em uma rede virtual do Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Ao promover o servidor à função de controlador de domínio, forneça o nome do mesmo domínio que está sendo usado no site primário.

Depois disso, você deverá [reconfigurar o servidor DNS para a rede virtual](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) para usar o servidor DNS no Azure
  
![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

##Considerações sobre o failover de teste
O failover de teste é realizado em uma rede isolada da rede de produção para que não ocorra qualquer impacto na carga de trabalho de produção. A maioria dos aplicativos também exige a presença de um controlador de domínio e de um servidor DNS para funcionar. Portanto, antes de um aplicativo sofrer o failover, será necessário criar um controlador de domínio na rede isolada para usá-lo para o failover de teste. A maneira mais fácil de fazer isso é habilitar primeiro a proteção na máquina virtual do controlador de domínio/DNS usando o ASR e antes de disparar o failover de teste do plano de recuperação do aplicativo, dispare o failover de teste da máquina virtual do controlador de domínio. A seguir, uma orientação passo a passo para a execução do failover de teste:

1. Habilite a proteção na máquina virtual do controlador de domínio/DNS como faria para qualquer outra máquina virtual.
2. Crie uma rede isolada. Qualquer rede virtual criada no Azure é isolada por padrão de outras redes. Recomendamos que o intervalo IP dessa rede seja o mesmo de sua rede de produção. Não habilite a conectividade site a site nessa rede.
3. Forneça o IP do DNS na rede criada na etapa acima como o IP que você espera que a VM do DNS obtenha. Se você estiver usando o Azure como o site de DR, forneça o IP da VM que será usada no failover na configuração de "IP de destino" nas propriedades da VM. Se o seu site de DR for local e você estiver usando o DHCP, execute a instrução fornecida aqui para [configurar o DNS e o DHCP para o failover de teste](site-recovery-failover.md#prepare-dhcp) 

>[AZURE.NOTE]O IP fornecido a uma máquina virtual em um failover de teste é o mesmo IP que ela receberia executando um failover planejado ou não planejado, considerando que esse IP está disponível na rede de failover de teste. Se o mesmo IP não estiver disponível na rede de failover de teste, a máquina virtual receberá alguns outros IP disponíveis na rede de failover de teste.

4. Acesse a máquina virtual do controlador de domínio e teste o failover na rede isolada. 
5. Execute o teste o failover do plano de recuperação do aplicativo.
6. Após a conclusão do teste, marque o failover de teste do trabalho de máquina virtual do controlador de domínio e o plano de recuperação com "Concluído" na guia trabalhos no ASR. 

###DNS em uma Máquina Virtual diferente como o Controlador de Domínio: 
Caso o DNS não esteja na mesma máquina virtual que o controlador de domínio, será necessário criar um DNS para o failover de teste. Caso eles estejam na mesma VM, você poderá ignorar esta seção. Você pode usar um servidor DNS atualizado e criar todas as zonas necessárias. Por exemplo, se o domínio do Active Directory for contoso.com, você poderá criar uma zona com o nome contoso.com. As entradas que correspondem ao Active Directory devem ser atualizadas no DNS. Faça isso da seguinte forma:

- Assegure-se de que as configurações a seguir estejam definidas antes que outra máquina virtual apareça no plano de recuperação:
	- A zona deve ser nomeada depois do nome raiz da floresta.
	- A zona deve ter backup em arquivo.
	- A zona deve ser habilitada para atualizações seguras e não seguras.
	- O resolvedor da máquina virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual do DNS.
- Execute o seguinte comando na máquina virtual do controlador de domínio Diretório: nltest /dsregdns

- **Adicionar zona**: use o exemplo de script a seguir para adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para si própria no DNS:

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##Resumo
Com o Azure Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para seu AD. Você poderá iniciar o failover em segundos e de qualquer lugar no caso de uma interrupção e fazer o AD funcionar em alguns minutos. Caso você tenha um AD para vários aplicativos, como o SharePoint e SAP, em seu site primário e decidir aplicar o failover em todo o site, aplique o failover no AD usando o ASR primeiro e aplique o failover nos outros aplicativos usando planos de recuperação específicos ao aplicativo.


Consulte [Diretrizes para a carga de trabalho de recuperação de site](../site-recovery/site-recovery-workload.md) para obter mais detalhes sobre como proteger várias cargas de trabalho usando o ASR.

<!---HONumber=Oct15_HO3-->