---
title: Perguntas frequentes sobre a Rede Virtual do Azure | Microsoft Docs
description: Respostas para as perguntas mais frequentes sobre as redes virtuais do Microsoft Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: 2802a725bca7f63f6956293048b0e854ebfb59b5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42141862"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a rede virtual do Azure (FAQ)

## <a name="virtual-network-basics"></a>Noções básicas sobre redes virtuais

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma Rede Virtual (VNet) do Azure?
Uma Rede Virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você pode usar redes virtuais para provisionar e gerenciar VPNs (redes virtuais privadas) no Azure e, se desejar, vincular as redes virtuais no Azure a outras redes virtuais no Azure ou à sua infraestrutura de TI local para criar soluções híbridas e entre locais. Cada VNet que você cria tem seu próprio bloco CIDR e pode ser vinculada a outras redes virtuais e redes locais, desde que os blocos CIDR não se sobreponham. Você também controla as configurações do servidor DNS para redes virtuais e a segmentação da VNet em sub-redes.

Use redes virtuais para:

* Crie uma VNet dedicada apenas para nuvem privada Às vezes, você não precisa de uma configuração entre locais para sua solução. Quando você cria uma redes virtuais, seus serviços e VMs na rede virtual podem se comunicar de forma direta e segura entre si na nuvem. Adicionalmente, é possível configurar conexões de ponto de extremidade para VMs e serviços que exigem comunicação via Internet, como parte de sua solução.
* Expanda seu data center com segurança Com as redes virtuais, você pode criar VPNs site a site (S2S) tradicionais para escalar com segurança a capacidade do data center. As VPNs S2S usam IPSEC para fornecer uma conexão segura entre o gateway de VPN corporativo e o Azure.
* Habilite cenários de nuvem híbrida As VNets proporcionam flexibilidade para dar suporte a uma variedade de cenários de nuvem híbrida. É possível conectar com segurança aplicativos baseados em nuvem a qualquer tipo de sistema local, como mainframes e sistemas Unix.

### <a name="how-do-i-get-started"></a>Como começar?
Acesse a [documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) para saber como começar a usar. Este conteúdo fornece informações gerais e de implantação para todos os recursos de VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar redes virtuais sem conectividade entre locais?
Sim. É possível utilizar uma VNet sem conectá-la às suas instalações. Por exemplo, você pode executar controladores de domínio do Microsoft Windows Server Active Directory e farms do SharePoint exclusivamente em uma VNet do Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso realizar uma otimização de WAN entre redes virtuais ou entre uma rede virtual e meu data center local?
Sim. Você pode implantar um [dispositivo virtual de rede de otimização de WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) de vários fornecedores por meio do Marketplace do Azure.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quais ferramentas eu uso para criar uma redes virtuais?
Você pode usar as seguintes ferramentas para criar ou configurar uma rede virtual:

* Portal do Azure
* PowerShell
* CLI do Azure
* Um arquivo de configuração de rede (netcfg - somente para Redes Virtuais clássicas). Consulte o artigo [Configurar uma VNet usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quais intervalos de endereço posso usar em minhas redes virtuais?
Qualquer intervalo de endereços IP definido na [RFC 1918](http://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos em minhas redes virtuais?
Sim. Para obter mais informações sobre intervalos de endereços IP públicos, consulte [Criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network). Os endereços IP públicos não são acessíveis diretamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Há um limite para o número de sub-redes na minha VNet?
Sim. Consulte [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter detalhes. Espaços de endereço de sub-rede não podem se sobrepor.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?
Sim. O Azure reserva alguns endereços IP em cada sub-rede. Os primeiros e últimos endereços IP de cada sub-rede são reservados para conformidade de protocolo, juntamente com os endereços x.x.x.1-x.x.x.3 de cada sub-rede, que são utilizados para serviços do Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Que tamanho, máximo e mínimo, as redes virtuais e sub-redes podem ter?
A sub-rede menor com suporte é / 29 e a maior é /8 (utilizando definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso levar minhas VLANs para o Azure usando redes virtuais?
Não. As v são sobreposições da Camada 3. O Azure não oferece suporte a nenhuma semântica da Camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar políticas de roteamento personalizadas nas minhas redes virtuais e sub-redes?
Sim. É possível criar uma tabela de rota e associá-la a uma sub-rede. Para obter mais informações sobre o roteamento no Azure, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>As redes virtuais oferecem suporte ao multicast ou à difusão?
Não. Não há suporte para difusão ou multicast.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos posso usar nas redes virtuais?
Você pode usar protocolos TCP, UDP e ICMP TCP/IP em redes virtuais. O Unicast tem suporte em VNets, com exceção do Protocolo DHCP via Unicast (porta de origem UDP/68 / porta de destino UDP/67). Pacotes encapsulados de IP em IP, multicast, difusão e pacotes de Encapsulamento de Roteamento Genérico (GRE) são bloqueados nas redes virtuais. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso executar ping em meus roteadores padrão em uma rede virtual?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar tracert para diagnosticar a conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois que a rede virtual é criada?
Sim. As sub-redes podem ser adicionadas às VNets a qualquer momento, desde que o alcance do endereço de sub-rede não faça parte de outra sub-rede e tenha espaço disponível no intervalo de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criá-la?
Sim. Você poderá adicionar, remover, expandir ou reduzir uma sub-rede se não houver VMs ou serviços implantados nela.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois de criá-las?
Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por uma rede virtual.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se eu estiver executando meus serviços em uma VNet, posso conectar-me à internet?
Sim. Todos os serviços implantados dentro de uma VNet podem conectar-se à internet. Para saber mais sobre as conexões de internet de saída no Azure, consulte [Conexões de Saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você quiser conectar a entrada a um recurso implantado através do Gerenciador de Recursos, o recurso deverá ter um endereço IP público atribuído a ele. Para saber mais sobre endereços IP públicos, consulte [Endereços IP públicos](virtual-network-public-ip-address.md). Cada Serviço de Nuvem do Azure implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você define pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para máquinas virtuais para permitir que esses serviços aceitem conexões da Internet.

### <a name="do-vnets-support-ipv6"></a>As redes virtuais oferecem suporte ao IPv6?
Não. Não é possível usar o IPv6 com redes virtuais neste momento. No entanto, é possível atribuir endereços IPv6 aos balanceadores de carga do Azure para balancear cargas de máquinas virtuais. Para obter detalhes, consulte [Visão geral do IPv6 para Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Uma rede virtual pode abranger regiões?
Não. A rede virtual é limitada a uma única região. Todavia, uma rede virtual abrange zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, confira [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É possível conectar redes virtuais em diferentes regiões com emparelhamento de rede virtual. Para obter detalhes, consulte [Visão geral do emparelhamento de rede virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso conectar uma rede virtual a outra rede virtual no Azure?
Sim. É possível conectar uma VNet a outra VNet, utilizando:
- **Emparelhamento de rede virtual**: para obter detalhes, consulte [Visão geral do emparelhamento de VNet](virtual-network-peering-overview.md)
- **Um Gateway de VPN do Azure**: Para obter detalhes, consulte [Configurar uma conexão VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções DNS para redes virtuais?
Use a tabela de decisão na página [Resolução de nome para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) , que orientará você por todas as opções DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para uma rede virtual?
Sim. Você pode especificar endereços IP do servidor DNS nas configurações da rede virtual. A configuração é aplicada como o(s) servidor(es) DNS padrão a todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Referência [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar meus servidores DNS depois de ter criado a rede?
Sim. Você pode alterar a lista de servidores DNS para sua rede virtual a qualquer momento. Se você alterar sua lista de servidores DNS, será preciso reiniciar cada uma das VMs em sua rede virtual para que elas selecionem o novo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é o DNS fornecido pelo Azure? Ele funciona com redes virtuais?
O DNS fornecido pelo Azure é um serviço DNS multilocatário oferecido pela Microsoft. O Azure registra todas as VMs e instâncias de função do serviço de nuvem nesse serviço. Esse serviço fornece resolução de nome por nome de host para VMs e instâncias de função contidas no mesmo serviço de nuvem e por FQDN para VMs e instâncias de função na mesma rede virtual. Para saber mais sobre DNS, consulte [Resolução de nomes para instâncias da função dos Serviços de Nuvem e VMs](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existe uma limitação para os primeiros 100 serviços de nuvem em uma VNet para resolução de nome entre locatários usando DNS fornecido pelo Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplicará.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Eu posso substituir minhas configurações DNS com base em serviço de nuvem ou por VM?
Sim. É possível configurar servidores DNS por VM ou serviço de nuvem para substituir as configurações de rede padrão. No entanto, é recomendável utilizar o DNS em toda a rede tanto quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso colocar meu próprio sufixo DNS?
Não. Você não pode especificar um sufixo DNS personalizado para suas redes virtuais.

## <a name="connecting-virtual-machines"></a>Conexão de máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso implantar VMs em uma rede virtual?
Sim. Todas as interfaces de rede (NIC) anexadas a uma VM implantada por meio do modelo de implantação do Gerenciador de Recursos devem estar conectadas a uma rede virtual. VMs implantadas por meio do modelo de implantação clássico podem ser conectadas a uma rede virtual.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir a VMs?
* **Privado:** atribuído a cada NIC em cada VM. O endereço é atribuído usando o método estático ou dinâmico. Os endereços IP privados são atribuídos do intervalo que você especificou nas configurações de sub-rede da VNet. Recursos implantados por meio do modelo de implantação clássico recebem endereços IP privados mesmo que não estejam conectados a uma VNet. O comportamento do método de alocação é diferente, dependendo se um recurso foi implantado com o modelo de implantação do Azure Resource Manager ou clássico: 

  - **Azure Resource Manager**: um endereço IP privado atribuído com o método dinâmico ou estático permanece atribuído a uma máquina virtual (Gerenciador de Recursos) até que o recurso seja excluído. A diferença é que você seleciona o endereço a ser atribuído quando usar estático e o Azure escolhe quando usar dinâmico. 
  - **Clássico**: um endereço IP privado atribuído com o método dinâmico poderá alterar quando uma VM da máquina virtual (clássica) é reiniciada após ter ficado no estado parado (desalocado). Se você precisar garantir que o endereço IP privado de um recurso implantado através do modelo de implantação clássico nunca altere, atribua um endereço IP privado com o método estático.

* **Público:** opcionalmente atribuído a NICs anexadas a VMs implantadas por meio do modelo de implantação do Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estática ou dinâmica. Todas as instâncias de função de VMs e Serviços de Nuvem implantadas por meio do modelo de implantação clássico existem dentro de um serviço de nuvem, ao qual é atribuído um endereço IP endereço virtual IP (VIP) *dinâmico* e público. Um endereço IP público e *estático*, chamado de [endereço IP Reservado](virtual-networks-reserved-public-ip.md), pode ser atribuído como VIP. Você pode atribuir endereços IP públicos a instâncias de função de VMs ou serviços de nuvem individuais implantados por meio do modelo de implantação clássico. Eles são chamados de [endereços IP públicos em nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para uma VM que vou criar mais tarde?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado estiver disponível, será atribuído a uma VM ou instância de função pelo servidor DHCP. A VM pode ou não ser aquela à qual você deseja que o endereço IP privado seja atribuído. No entanto, você pode alterar o endereço IP privado de uma VM já criada para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privado mudam para VMs em uma rede virtual?
Isso depende. Se a VM foi implantada por meio do Resource Manager, não, independentemente se o endereço IP foi atribuído com o método de alocação estática ou dinâmica. Se a VM foi implantada através do modelo de implantação clássico, os endereços IP dinâmicos poderão alterar quando uma VM for iniciada após ter ficado no estado parado (desalocado). O endereço é liberado de uma VM implantada através de um modelo de implantação quando a VM é excluída.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir endereços IP a NICs manualmente dentro do sistema operacional da VM?
Sim, mas não é recomendado, a menos que seja necessário, como quando atribuir vários endereços IP a uma máquina virtual. Para obter detalhes, consulte [Adicionar vários endereços IP a uma máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Se o endereço IP atribuído a um NIC do Azure anexado a uma VM alterar, e o endereço IP dentro do sistema operacional da VM for diferente, você perderá conectividade com a VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar um slot de implantação no Serviço de Nuvem ou desligar uma VM do sistema operacional, o que acontecerá com os endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos ao slot de implantação do Serviço de Nuvem ou à VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Eu posso mover VMs de uma sub-rede para outra sub-rede em uma VNet sem reimplantação?
Sim. Você pode encontrar mais informações no artigo [Como mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para minha VM?
Não. Um endereço MAC não pode ser configurado estaticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecerá o mesmo para a minha VM depois de criado?
Sim, o endereço MAC continua o mesmo para uma VM implantada por meio do Gerenciador de Recursos e dos modelos de implantação clássicos até que esta seja excluída. Anteriormente, o endereço MAC era lançado se a máquina virtual fosse interrompida (desalocada), mas agora o endereço MAC é mantido mesmo quando a VM está no estado desalocado.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso me conectar à internet de uma VM em uma rede virtual?
Sim. Todas as instâncias de função de VMs e Serviços de Nuvem implantados em uma rede virtual podem se conectar à internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que se conectam a redes virtuais

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso usar os Aplicativos Web do Serviço de Aplicativo do Azure em uma rede virtual?
Sim. Você pode implantar Aplicativos Web em uma rede virtual usando o ASE (Ambiente do Serviço de Aplicativo). Se você tiver uma conexão ponto a site configurada para sua VNet, todos os aplicativos Web poderão conectar-se de forma segura e acessar recursos na VNet. Para obter mais informações, consulte os seguintes artigos:

* [Criando Aplicativos Web em um Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Usando conexões híbridas e integração de rede virtual com Aplicativos Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implantar Serviços de Nuvem com funções web e de trabalho (PaaS) em uma rede virtual?
Sim. Você pode implantar instâncias de função de Serviços de Nuvem em redes virtuais. Para fazer isso, especifique o nome e os mapeamentos de função/sub-rede na seção de configuração de rede da sua configuração de serviço. Não é preciso atualizar nenhum dos binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Posso conectar um Conjunto de Dimensionamento de Máquinas Virtuais (VMSS) a uma rede virtual?
Sim. Você precisa conectar um VMSS a uma rede virtual.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Há uma lista completa dos serviços do Azure, dos quais eu posso implantar recursos em uma VNet?
Sim. Para obter detalhes, consulte [Integração de rede virtual para os serviços do Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Quais recursos de PaaS do Azure eu posso restringir o acesso de uma VNet?

Recursos implantados através de alguns serviços de PaaS do Azure (como Armazenamento do Microsoft Azure e Banco de Dados SQL do Microsoft Azure) podem restringir o acesso de rede a recursos somente em uma VNet através do uso de pontos de extremidade de serviço de rede virtual. Para obter detalhes, consulte [Visão geral dos pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mover meus serviços para dentro e fora das redes virtuais?
Não. Não é possível mover serviços para dentro e fora das redes virtuais. Para mover um recurso para outra VNet, você deverá excluir e reimplantar o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>Qual é o modelo de segurança para redes virtuais?
As VNets são isoladas uma da outra e outros serviços hospedados na infraestrutura do Azure. Uma rede virtual é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos conectados à rede virtual?
Sim. Você pode aplicar [Grupos de Segurança de Rede](security-overview.md) a sub-redes individuais em uma rede virtual, NICs conectados a uma rede virtual ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar um firewall entre os recursos conectados à rede virtual?
Sim. Você pode implantar [dispositivos virtuais de rede de firewall](https://azure.microsoft.com/marketplace/?term=firewall) de vários fornecedores por meio do Marketplace do Azure.

### <a name="is-there-information-available-about-securing-vnets"></a>Existem informações disponíveis sobre como proteger redes virtuais?
Sim. Para obter detalhes, consulte [Visão geral da segurança de rede do Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerenciar redes virtuais usando código?
Sim. Você pode usar APIs REST para VNets nos modelos de implantação do [Azure Resource Manager](/rest/api/virtual-network) e [clássico (Gerenciamento de Serviços)](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Há suporte a ferramentas para redes virtuais?
Sim. Saiba mais sobre como usar:
- O Portal do Azure para implantar redes virtuais por meio dos modelos de implantação do [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e [clássico](virtual-networks-create-vnet-classic-pportal.md).
- O PowerShell para gerenciar redes virtuais implantadas por meio dos modelos de implantação do [Gerenciador de Recursos](/powershell/module/azurerm.network) e [clássico](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).
- A CLI (interface de linha de comando) do Azure para implantar e gerenciar VNets implantadas através dos modelos de implantação do [Azure Resource Manager](/cli/azure/network/vnet) e [clássico](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources).  

## <a name="vnet-peering"></a>Emparelhamento VNet

### <a name="what-is-vnet-peering"></a>O que é emparelhamento VNet?
O emparelhamento VNet (ou emparelhamento de rede virtual) permite que você conecte redes virtuais. Uma conexão de emparelhamento VNet entre redes virtuais permite rotear o tráfego entre elas de modo privado por meio de endereços IPv4. As máquinas virtuais nas VNets emparelhadas podem se comunicar entre si, como se elas estivessem na mesma rede. Essas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecido como Emparelhamento VNet Global). As conexões de emparelhamento VNet também podem ser criadas entre assinaturas do Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>É possível criar uma conexão de emparelhamento para uma VNet em uma região diferente?
Sim. O emparelhamento VNet global permite emparelhar VNets em diferentes regiões. O emparelhamento VNet global está disponível em todas as regiões públicas do Azure. Não é possível emparelhar globalmente das regiões públicas do Azure para nuvens nacionais. O emparelhamento global não está atualmente disponível nas nuvens nacionais.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>É possível habilitar emparelhamento VNet se as redes virtuais pertencerem a assinaturas em diferentes locatários do Azure Active Directory?
Atualmente, não é possível estabelecer Emparelhamento VNet (local ou global) se as assinaturas pertencerem a diferentes locatários do Azure Active Directory.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A conexão de emparelhamento VNet está no estado *Iniciado*, por que não consigo conectar?
Se a conexão de emparelhamento estiver em um estado Iniciado, isso significa que você criou apenas um vínculo. Um vínculo bidirecional deve ser criado para estabelecer uma conexão com êxito. Por exemplo, para emparelhar VNet A a VNet B, um vínculo deve ser criado da VNetA para VNetB e da VNetB para VNetA. A criação de ambos os vínculos irá alterar o estado para *Conectado.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Minha conexão de emparelhamento VNet está no estado *Desconectado*, por que não consigo criar uma conexão de emparelhamento?
Se sua conexão de emparelhamento de rede virtual estiver em um estado desconectado, isso significa que um dos links criados foi excluído. Para restabelecer uma conexão de emparelhamento, você precisará excluir o link e recriar.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Eu posso emparelhar minha VNet com uma VNet em uma assinatura diferente?
Sim. É possível emparelhar VNets entre assinaturas e entre regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Eu posso emparelhar duas VNets com intervalos de endereços sobrepostos ou correspondentes?
Não. Espaços de endereço não devem sobrepor para habilitar Emparelhamento VNet.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto custa para fazer vínculos de emparelhamento VNet?
Não há encargos para criar uma conexão de emparelhamento VNet. É cobrada a transferência de dados entre as conexões de emparelhamento. [Consulte aqui](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>O tráfego de emparelhamento VNet é criptografado?
Não. O tráfego entre recursos em VNets emparelhadas é privado e isolado. Ele permanece completamente no backbone da Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Por que a conexão de emparelhamento está em um estado desconectado?
As conexões de emparelhamento VNet entram no estado *Desconectado* quando um vínculo de emparelhamento VNet é excluído. É necessário excluir ambos os vínculos para restabelecer uma conexão de emparelhamento com êxito.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se emparelhar VNetA para VNetB e emparelhar VNetB para VNetC, isso significa que VNetA e VNetC estão emparelhadas?
Não. Não há suporte para emparelhamento transitivo. É necessário emparelhar a VNetA e VNetC para que isso seja realizado.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Há alguma limitação de largura de banda para conexões de emparelhamento?
Não. O emparelhamento VNet, seja local ou global, não impõe restrições de largura de banda. A largura de banda é limitada apenas pela VM ou pelo recurso de computação.

