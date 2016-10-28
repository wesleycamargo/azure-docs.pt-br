<properties
	pageTitle="Proteger o Active Directory e o DNS com o Azure Site Recovery | Microsoft Azure"
	description="Este artigo descreve como implementar uma solução de recuperação de desastre para o Active Directory usando o Azure Site Recovery."
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
	ms.date="08/31/2016"
	ms.author="pratshar"/>

# Proteger o Active Directory e o DNS com o Azure Site Recovery

Aplicativos empresariais como o SharePoint, o Dynamics AX e o SAP dependem do Active Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando você cria uma solução de recuperação de desastre para aplicativos, é importante se lembrar de que você precisa proteger e recuperar o Active Directory e o DNS antes dos outros componentes de aplicativo, para garantir que tudo funcione corretamente quando ocorrer um desastre.

O Site Recovery é um serviço do Azure que fornece recuperação de desastre por meio da orquestração de replicação, failover e recuperação de máquinas virtuais. O Site Recovery dá suporte a vários cenários de replicação, com o intuito de proteger e executar failover de forma consistente de máquinas virtuais e aplicativos em nuvens privadas, públicas ou de hoster.

Com a Recuperação de Site, você pode criar um plano totalmente automatizado de recuperação de desastre para o Active Directory. Quando ocorrem interrupções, você pode iniciar um failover em poucos segundos de qualquer lugar para que o Active Directory esteja em funcionamento em alguns minutos. Se você implantou o Active Directory para vários aplicativos, como o SharePoint e o SAP, em seu site primário e deseja executar failover de todo o site, é possível executar o failover do Active Directory primeiro usando a Recuperação de Site e depois executar o failover dos outros aplicativos que usam planos de recuperação específicos do aplicativo.

Este artigo explica como criar uma solução de recuperação de desastre para o Active Directory, como executar failovers de teste planejados e não planejados usando um plano de recuperação de um clique, as configurações com suporte e os pré-requisitos. Antes de começar, você deve estar familiarizado com o Active Directory e o Azure Site Recovery.

Há duas opções recomendadas com base na complexidade do seu ambiente.

### Opção 1

Se você tiver uma pequena quantidade de aplicativos e um único controlador de domínio, e desejar executar failover de todo o site, é recomendável usar a Recuperação de Site para replicar o controlador de domínio para o site secundário (se você estiver executar failover no Azure ou em um site secundário). A mesma máquina virtual replicada também pode ser usada para o failover de teste.

### Opção 2

Se você tiver um grande número de aplicativos e houver mais de um controlador de domínio no ambiente ou se você planejar executar failover de alguns aplicativos por vez, será recomendável que, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, você também configure um controlador de domínio adicional no site de destino (Azure ou um datacenter local secundário).

>[AZURE.NOTE] Mesmo que você esteja implementando a Opção 2, para executar um failover de teste, você ainda precisará replicar o controlador de domínio usando a Recuperação de Site. Leia [considerações sobre failover de teste](#considerations-for-test-failover) para obter mais informações.


As seções a seguir explicam como habilitar a proteção para um controlador de domínio na Recuperação de Site e como configurar um controlador de domínio no Azure.


## Pré-requisitos

- Uma implantação local do servidor DNS e do Active Directory.
- Um cofre dos Serviços do Azure Site Recovery em uma assinatura do Microsoft Azure.
- Se estiver replicando para o Azure, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs para garantir que sejam compatíveis com as VMs do Azure e com os Serviços do Azure Site Recovery.


## Habilitar a proteção com a Recuperação de Site


### Proteger a máquina virtual

Habilite a proteção da máquina virtual do DNS/controlador de domínio na Site Recovery. Defina configurações da Recuperação de Site de acordo com o tipo de máquina virtual (Hyper-V ou VMware). É recomendável uma frequência de replicação consistente de falha de 15 minutos.

###Definir configurações de rede da máquina virtual

Para a máquina virtual do controlador de domínio/DNS, defina as configurações de rede da Recuperação de Site de modo que a VM seja anexada à rede correta após um failover. Por exemplo, se você estiver replicando VMs do Hyper-V no Azure, é possível selecionar a VM na nuvem do VMM ou no grupo de proteção para definir as configurações de rede, conforme mostrado abaixo

![Configurações de rede da VM](./media/site-recovery-active-directory/VM-Network-Settings.png)

## Proteger o Active Directory com a replicação do Active Directory

### Proteção site a site

Crie um controlador de domínio no site secundário e especifique o nome do mesmo domínio que está sendo usado no site primário quando promover o servidor para uma função de controlador de domínio. Você pode usar o snap-in dos **Sites e Serviços do Active Directory** para definir as configurações no objeto de link de site ao qual os sites serão adicionados. Ao definir as configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e com que frequência. Veja [Agendando a replicação entre sites](https://technet.microsoft.com/library/cc731862.aspx) para obter mais detalhes.

###Proteção Site ao Azure

Siga estas instruções para [criar um controlador de domínio em uma rede virtual do Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no site primário.

Depois disso, [reconfigure o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) para usar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

## Considerações sobre failover de teste

O failover de teste ocorre em uma rede isolada da rede de produção para que não ocorra impactos nas cargas de trabalho de produção.

A maioria dos aplicativos também exige a presença de um controlador de domínio e um servidor DNS para funcionar; portanto, antes do failover do aplicativo, um controlador de domínio precisa ser criado na rede isolada a ser usada para failover de teste. A maneira mais fácil de fazer isso é habilitar a proteção na máquina virtual do DNS/controlador de domínio com o Site Recovery e executar um failover de teste dessa máquina virtual, antes de executar um failover de teste do plano de recuperação para o aplicativo. Veja como fazer isso:

1. Habilite a proteção na Recuperação de Site da máquina virtual do DNS/controlador de domínio.
2. Crie uma rede isolada. Qualquer rede virtual criada no Azure é isolada por padrão de outras redes. Recomendamos que o intervalo de endereços IP dessa rede seja o mesmo de sua rede de produção. Não habilite a conectividade site a site nessa rede.
3. Forneça um endereço IP de DNS na rede criada, como o endereço IP que você espera que a máquina virtual do DNS obtenha. Se estiver replicando para o Azure, forneça o endereço IP da VM que será usada no failover na configuração **IP de Destino** nas propriedades da VM. Se você estiver replicando para outro site local e estiver usando o DHCP, siga as instruções para [configurar o DNS e o DHCP para failover de teste](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] O endereço IP alocado a uma máquina virtual durante um failover de teste é o mesmo endereço IP que ela obteria durante um failover planejado ou não planejado se o endereço IP estivesse disponível na rede de failover de teste. Caso contrário, a máquina virtual receberá um endereço IP diferente que está disponível na rede de failover de teste.

4. Na máquina virtual do controlador de domínio, execute um failover de teste na rede isolada. Use o ponto de recuperação consistente de aplicativo mais recente disponível da máquina virtual do controlador de domínio para fazer o failover de teste.
5. Execute um failover de teste para o plano de recuperação do aplicativo.
6. Após a conclusão do teste, marque o trabalho de failover de teste da máquina virtual do controlador de domínio e do plano de recuperação como "Concluído" na guia **Trabalhos** do portal do Site Recovery.

### DNS e controlador de domínio em computadores diferentes

Caso o DNS não esteja na mesma máquina virtual que o controlador de domínio, será necessário criar uma VM do DNS para o failover de teste. Caso eles estejam na mesma VM, você pode ignorar esta seção.

Você pode usar um servidor DNS atualizado e criar todas as zonas necessárias. Por exemplo, se o domínio do Active Directory for contoso.com, é possível criar uma zona DNS com o nome contoso.com. As entradas que correspondem ao Active Directory devem ser atualizadas no DNS, da seguinte maneira:

1. Assegure-se de que essas configurações estão definidas antes que outra máquina virtual apareça no plano de recuperação:

	- A zona deve ser nomeada depois do nome raiz da floresta.
	- A zona deve ter backup em arquivo.
	- A zona deve ser habilitada para atualizações seguras e não seguras.
	- O resolvedor da máquina virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual do DNS.

2. Execute o seguinte comando na máquina virtual do controlador de domínio:

	`nltest /dsregdns`

3. Adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para ela no DNS:

	    dnscmd /zoneadd contoso.com  /Primary
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
	    dnscmd /config contoso.com /allowupdate 1


## Próximas etapas

Leia [Quais cargas de trabalho posso proteger?](../site-recovery/site-recovery-workload.md) para saber mais sobre como proteger cargas de trabalho corporativas com o Azure Site Recovery.

<!---HONumber=AcomDC_0831_2016-->