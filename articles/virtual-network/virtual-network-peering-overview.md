---
title: Emparelhamento de rede virtual do Azure | Microsoft Docs
description: Saiba mais sobre o emparelhamento de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: narayan
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bfba85160cd02baa337881653dbe6582d10ba073
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017


---
<a id="virtual-network-peering" class="xliff"></a>

# Emparelhamento de rede virtual
O emparelhamento de rede virtual permite que você se conecte duas VNets na mesma região por meio da rede de backbone do Azure. Uma vez emparelhadas, as duas redes virtuais aparecerão como uma para fins de conectividade. As duas redes virtuais ainda são gerenciadas como recursos separados, mas as máquinas virtuais nas redes virtuais emparelhadas podem se comunicar diretamente usando o endereço IP privado.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura do Azure, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual. Alguns dos benefícios do uso do emparelhamento de rede virtual incluem:

* Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de usar recursos, como dispositivos de rede e gateways de VPN como pontos de trânsito na rede virtual emparelhada.
* A capacidade de emparelhar redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager ou emparelhar uma rede virtual criada pelo Gerenciador de Recursos com uma rede virtual criada por meio do modelo de implantação clássico. Leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para saber mais sobre as diferenças entre os dois modelos de implantação do Azure.

Requisitos e aspectos fundamentais de emparelhamento de rede virtual:

* As redes virtuais emparelhadas devem existir na mesma região do Azure. Você pode se conectar a redes virtuais em diferentes regiões do Azure usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* As redes virtuais emparelhadas devem ter espaços de endereço IP não sobrepostos.
* Espaços de endereço não podem ser adicionados ao ou excluídos de uma rede virtual depois que ela é emparelhada com outra rede virtual.
* O emparelhamento de rede virtual é entre duas redes virtuais. Não há nenhuma relação transitiva derivada entre emparelhamentos. Por exemplo, se virtualNetworkA está emparelhada com virtualNetworkB e virtualNetworkB está emparelhada com virtualNetworkC, virtualNetwork *não* está emparelhada com virtualNetworkC.
* Você pode emparelhar redes virtuais existentes em duas assinaturas diferentes, desde que um usuário com privilégios de ambas as assinaturas autorize o emparelhamento e as assinaturas estejam associadas ao mesmo locatário do Azure Active Directory. Você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuais em assinaturas associadas a locatários diferentes do Active Directory.
* Redes virtuais podem ser emparelhadas se ambas são criadas por meio do modelo de implantação do Gerenciador de Recursos ou se uma é criada por meio do modelo de implantação do Gerenciador de Recursos e a outra é criada por meio do modelo de implantação clássico. No entanto, duas redes virtuais criadas por meio do modelo de implantação clássico não podem ser emparelhadas uma à outra. Ao emparelhar redes virtuais criadas por meio de diferentes modelos de implantação, ambas as redes virtuais devem existir na *mesma* assinatura. A capacidade de emparelhar redes virtuais criadas por meio de diferentes modelos de implantação que existem em assinaturas *diferentes* está em versão de **versão prévia**. Leia o artigo [Criar um emparelhamento de rede virtual](virtual-network-create-peering.md#different-subscriptions-different-deployment-models) para obter mais detalhes.
* Embora a comunicação entre máquinas virtuais em redes virtuais emparelhadas não tenha restrição de largura de banda adicional, há uma largura de banda de rede máxima, dependendo do tamanho da máquina virtual que ainda é aplicável. Para saber mais sobre a largura de banda de rede máxima para diferentes tamanhos de máquina virtual, leia os artigos sobre tamanhos de máquina virtual do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

![Emparelhamento básico de redes virtuais](./media/virtual-networks-peering-overview/figure01.png)

<a id="connectivity" class="xliff"></a>

## Conectividade
Depois que duas redes virtuais são emparelhadas, as máquinas virtuais ou funções de Serviços de Nuvem na rede virtual podem se conectar diretamente a outros recursos conectados à rede virtual emparelhada. As duas redes virtuais terão conectividade total no nível do IP.

A latência de rede para uma viagem de ida e volta entre duas máquinas virtuais em redes virtuais emparelhadas, é a mesma para uma viagem de ida e volta em uma mesma rede virtual. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente por meio da infraestrutura de back-end do Azure, não por um gateway.

As máquinas virtuais conectadas a uma rede virtual podem acessar os pontos de extremidade de balanceamento de carga interno na rede virtual emparelhada. Grupos de segurança de rede podem ser aplicados em qualquer rede virtual a fim de bloquear o acesso a outras redes virtuais, ou sub-redes se for desejado.

Ao configurar o emparelhamento de rede virtual, você pode abrir ou fechar as regras de grupo de segurança de rede entre as redes virtuais. Se você abrir a conectividade total entre as redes virtuais emparelhadas (opção padrão), poderá aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas para bloquear ou negar o acesso específico. Para saber mais sobre grupos de segurança de rede, leia a [Visão geral dos grupos de segurança de rede](virtual-networks-nsg.md).

A resolução de nome DNS interna fornecida pelo Azure para as Máquinas virtuais não funcionam nas redes virtuais emparelhadas. As máquinas virtuais têm nomes DNS internos que podem ser resolvidos apenas na rede virtual local. No entanto, você pode configurar máquinas virtuais conectadas a redes virtuais emparelhadas como servidores DNS para uma rede virtual. Para obter mais detalhes, leia o artigo [Resolução de nome usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

<a id="service-chaining" class="xliff"></a>

## Encadeamento de serviços
Você pode configurar rotas definidas pelo usuário que apontam para máquinas virtuais em redes virtuais emparelhadas como o endereço IP "próximo salto" para habilitar o encadeamento de serviços. O encadeamento de serviços permite que você direcione o tráfego de uma rede virtual para uma solução virtual em uma rede virtual emparelhada através de rotas definidas pelo usuário.

Você também pode criar efetivamente ambientes do tipo hub e spoke, nos quais o hub pode hospedar componentes de infraestrutura, como um dispositivo de rede virtual. Todas as redes virtuais contadas podem emparelhar com a rede virtual do hub. O tráfego pode fluir por meio de dispositivos de rede virtual que estejam em execução na rede virtual do hub. Resumindo, o emparelhamento de redes virtuais permite que o endereço IP de próximo salto na tabela de rotas definida pelo usuário seja o endereço IP de uma máquina virtual na rede virtual emparelhada. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Visão geral de rotas definidas pelo usuário](virtual-networks-udr-overview.md).

<a id="gateways-and-on-premises-connectivity" class="xliff"></a>

## Gateways e conectividade local
Cada rede virtual, independentemente de estar emparelhada com outra rede virtual ou não, ainda pode ter seu próprio gateway e usá-lo para se conectar a uma rede local. Você também pode configurar a [conexão rede virtual para rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo que as redes virtuais estejam emparelhadas.

Quando as duas opções para a interconectividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as redes virtuais estiverem emparelhadas, você também poderá configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual pode ter apenas um gateway. O gateway pode ser um gateway local ou remoto (na rede virtual emparelhada), conforme mostrado na seguinte figura:

![Trânsito de emparelhamento VNet](./media/virtual-networks-peering-overview/figure02.png)

Não há suporte para o trânsito de gateway na relação de emparelhamento entre redes virtuais criadas por meio de diferentes modelos de implantação. Ambas as redes virtuais na relação de emparelhamento devem ter sido criadas pelo Gerenciador de Recursos para que um gateway de trânsito funcione.

Quando as redes virtuais que compartilham uma única conexão do Azure ExpressRoute forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, pela rede de backbone do Azure). Você ainda pode usar gateways locais em cada rede virtual para se conectar ao circuito local. Como alternativa, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

<a id="provisioning" class="xliff"></a>

## Provisionamento
O emparelhamento de rede virtual é uma operação privilegiada. É uma função separada sob o namespace da VirtualNetworks. Um usuário pode receber direitos específicos para autorizar o emparelhamento. Um usuário que tem acesso de leitura e gravação para a rede virtual herda automaticamente esses direitos.

Um usuário que é um administrador ou um usuário com privilégios da capacidade de emparelhamento pode iniciar uma operação de emparelhamento em outra rede virtual. Se houver uma solicitação correspondente para o emparelhamento no outro lado e outros requisitos forem atendidos, o emparelhamento será estabelecido.

<a id="limits" class="xliff"></a>

## limites
Há limites no número de emparelhamentos permitidos para uma única rede virtual. Para saber mais, confira os [Limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits).

<a id="pricing" class="xliff"></a>

## Preços
Há um custo nominal para tráfego de entrada e saída que utiliza um emparelhamento de rede virtual. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Próximas etapas

* Conclua o [tutorial de emparelhamento de rede virtual](virtual-network-create-peering.md)
* Saiba mais sobre todas as [configurações de emparelhamento de rede virtual e como alterá-las](virtual-network-manage-peering.md).

