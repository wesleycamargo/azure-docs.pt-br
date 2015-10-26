<properties
   pageTitle="Visão geral das redes virtuais do Azure (VNets)"
   description="Saiba mais sobre redes virtuais no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Visão geral da Rede Virtual

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. Você pode controlar as configurações de rede do Azure e definir blocos de endereço DHCP, configurações de DNS, políticas de segurança e roteamento. Você também pode segmentar sua VNet em sub-redes e implantar máquinas virtuais (VMs) IaaS e instâncias de função de PaaS do Azure, da mesma maneira que pode implantar máquinas físicas virtuais no datacenter local. Em essência, você pode expandir sua rede no Azure, trazendo seus próprios blocos de endereço IP.

Para entender melhor as VNets, dê uma olhada na figura abaixo, que mostra uma rede local simplificada.

![Rede local](./media/virtual-networks-overview/figure01.png)

A figura acima mostra uma rede local conectada à Internet pública por meio de um roteador. Também é possível ver um firewall entre o roteador e um DMZ hospedando um servidor DNS e um farm de servidores web. O farm de servidores web tem a carga balanceada por um balanceador de carga de hardware que é exposto à Internet e consome recursos da sub-rede interna. A sub-rede interna é separada do DMZ por outro firewall e hospeda servidores de controlador de domínio do Active Directory, servidores de banco de dados e servidores de aplicativos.

A mesma rede pode ser hospedada no Azure, conforme mostrado na figura abaixo.

![Rede Virtual do Azure](./media/virtual-networks-overview/figure02.png)

Observe como a infraestrutura do Azure assume a função do roteador, permitindo o acesso à Internet pública pela VNet sem a necessidade de configuração. Firewalls podem ser substituídos por grupos de segurança de rede (NSGs) aplicados a cada sub-rede. E balanceadores de carga físicos são substituídos por balanceadores de carga interno e voltado para a internet no Azure.

## Redes Virtuais

As VNets fornecem os seguintes serviços para VMs de IaaS e instâncias de função de PaaS de função implantadas nelas:

- **Isolamento**. As VNets são completamente isoladas umas das outras. Isso permite que você crie VNets separadas para desenvolvimento, teste e produção que usem os mesmos blocos de endereços CIDR.

- **Contenção**. As VNets não podem abranger múltiplas regiões do Azure.

    >[AZURE.NOTE]Há dois modos de implantação no Azure: clássico (também conhecido como gerenciamento de serviços) e o Gerenciador de Recursos do Azure (ARM). As VNets clássicos podem ser adicionados a um grupo de afinidade ou criados como VNet regional. Se você tiver uma VNet em um grupo de afinidade, convém [migrá-la para uma VNet regional](./virtual-networks-migrate-to-regional-vnet.md).

- **Acesso à Internet pública**. Todas as VMs de IaaS e instâncias de função de PaaS em uma VNet podem acessar a Internet pública por padrão. Você pode controlar o acesso usando grupos de segurança de rede (NSGs).

- **Acesso às VMs na VNet**. VMs de IaaS e instâncias de função de PaaS podem se conectar entre si na mesma VNet, mesmo se eles estiverem em sub-redes diferentes, sem a necessidade de configurar um gateway ou usar endereços IP públicos, reunindo os ambientes de PaaS e IaaS.

- **Resolução de nomes**. O Azure fornece resolução de nomes interna para VMs de IaaS e instâncias de função de PaaS implantadas na sua VNet. Você também pode implantar seus próprios servidores DNS e configurar a VNet para usá-los.

- **Conectividade**. As VNets podem ser conectadas entre si e até mesmo ao datacenter local, usando uma conexão de VPN site a site ou conexão de rota expressa. Para saber mais sobre gateways de VPN, consulte [Sobre gateways de VPN](./vpn-gateway-about-vpngateways.md). Para saber mais sobre a Rota Expressa, acesse [Visão geral técnica da Rota Expressa](./expressroute-introduction.md).

    >[AZURE.NOTE]Certifique-se de criar uma VNet antes de implantar qualquer VMs de IaaS ou instâncias de função de PaaS em seu ambiente do Azure. VMs baseadas em ARM exigem uma VNet e se você não especificar uma VNet existente, o Azure cria uma VNet padrão que pode ter conflito de bloco de endereços CIDR com sua rede local. Impossibilitando a conexão da VNet à sua rede local.

## Sub-redes

Você pode dividir a sua VNet em várias sub-redes para fins de organização e segurança. As sub-redes em uma VNet podem se comunicar entre si, sem nenhuma configuração adicional. Também é possível alterar as configurações de roteamento no nível de sub-rede e aplicar NSGs a sub-redes.

## Endereços IP

Há dois tipos de endereços IP atribuídos aos componentes no Azure: público e privado. As VMs de IaaS e instâncias de função de PaaS implantadas em uma sub-rede do Azure recebem automaticamente um endereço de IP privado em cada uma das suas NICs com base nos blocos de endereços CIDR atribuídos a suas sub-sedes. Também é possível atribuir um endereço IP público para suas VMs de IaaS e instâncias de função de PaaS.

Esses endereços IP são dinâmicos, o que significa que eles podem mudar a qualquer momento. Convém garantir que o endereço IP para determinados serviços permaneçam o mesmo, o tempo todo. Para isso, você pode reservar um endereço IP, tornando-o estático.

## Balanceadores de carga do Azure

Você pode usar dois tipos de balanceadores de carga no Azure:

- **Balanceador de carga externo** Você pode usar um balanceador de carga externo para fornecer alta disponibilidade para VMs de IaaS e instâncias de função de PaaS acessadas pela Internet pública.

- **Balanceador de carga interno** Você pode usar um balanceador de carga interno para fornecer alta disponibilidade para VMs de IaaS e instâncias de função de PaaS acessadas por outros serviços em sua VNET.

Para saber mais sobre o balanceamento de carga no Azure, visite [Visão geral do balanceador de carga](../load-balancer-overview.md).

## Grupos de segurança de rede (NSG)

Você pode criar NSGs para controlar o acesso de entrada e saída para interfaces de rede (NICs), VMs e sub-redes. Cada NSG contém uma ou mais regras que especificam se o tráfego está aprovado ou negado com base no endereço IP de origem, porta de origem, endereço IP de destino e porta de destino. Para saber mais sobre NSGs, acesse [O que é um grupo de segurança de rede](../virtual-networks-nsg.md).

## Dispositivos virtuais

Um dispositivo virtual é apenas outra VM em seu VNet que executa uma função de aplicativo baseada em software, como firewall, otimização de WAN ou detecção de intrusão. Você pode criar uma rota no Azure para direcionar o tráfego da VNet por meio de um dispositivo virtual para usar seus recursos.

Por exemplo, NSGs podem ser usados para fornecer segurança em sua VNet. No entanto, NSGs fornecem lista de controle de acesso (ACL) de camada 4 para pacotes de entrada e saída. Se você quiser usar um modelo de segurança de camada 7, é necessário usar um dispositivo de firewall.

Dispositivos virtuais dependem de [encaminhamento IP e rotas definidas pelo usuário](../virtual-networks-udr-overview.md).

## Próximas etapas

- [Criar uma VNet](../virtual-networks-create-vnet-arm-pportal.md) e sub-redes.
- [Criar uma VM em uma VNet](../virtual-machines-windows-tutorial.md).
- Saiba mais sobre [NSGs](../virtual-networks-nsg.md).
- Saiba mais sobre [balanceadores de carga](../load-balancer-overview.md).
- [Reservar um endereço IP interno.](../virtual-networks-reserved-private-ip.md)
- [Reservar um endereço IP público](../virtual-networks-reserved-public-ip.md).
- Saiba mais sobre [encaminhamento IP e rotas definidas pelo usuário](virtual-networks-udr-overview.md).

<!---HONumber=Oct15_HO3-->