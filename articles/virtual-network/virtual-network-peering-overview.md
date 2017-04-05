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
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Emparelhamento de rede virtual
O emparelhamento de VNet (rede virtual) permite que você se conecte duas VNets na mesma região por meio da rede de backbone do Azure. Quando emparelhadas, as duas VNets aparecem como uma para fins de conectividade. As duas VNets são gerenciadas como recursos separados, mas VMs (máquinas virtuais) nas VNets emparelhadas podem se comunicar entre si usando endereços IP privados.

O tráfego entre as VMs nas VNets emparelhadas é roteado por meio da infraestrutura do Azure, assim como o tráfego é roteado entre VMs na mesma VNet. Entre os benefícios de usar o emparelhamento VNet estão:

* Uma conexão da baixa latência e largura de banda alta entre os recursos em VNets diferentes.
* A capacidade de usar recursos, como dispositivos de rede e gateways de VPN como pontos de trânsito na VNet emparelhada.
* A capacidade de emparelhar VNets criadas por meio do modelo de implantação do Azure Resource Manager ou emparelhar uma VNet criada pelo Gerenciador de Recursos com uma VNet criada por meio do modelo de implantação clássico. Leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md) para saber mais sobre as diferenças entre os dois modelos de implantação do Azure.

Requisitos e principais aspectos do emparelhamento VNet:

* As VNets emparelhadas devem existir na mesma região do Azure.
* As VNets emparelhadas devem ter espaços de endereço IP não sobrepostos.
* O emparelhamento de VNet é entre duas VNets, mas não há uma relação transitiva derivada entre os emparelhamentos. Por exemplo, se VNetA é emparelhada com VNetB e VNetB é emparelhada com VNetC, VNetA *não* é emparelhada com VNetC.
* Você pode emparelhar VNets existentes em duas assinaturas diferentes, desde que um usuário com privilégios de ambas as assinaturas autorize o emparelhamento e as assinaturas estejam associadas ao mesmo locatário do Active Directory.
* VNets podem ser emparelhadas se ambas são criadas por meio do modelo de implantação do Gerenciador de Recursos ou se uma é criada por meio do modelo de implantação do Gerenciador de Recursos e a outra é criada por meio do modelo de implantação clássico. No entanto, duas VNets criadas por meio do modelo de implantação clássico não podem ser emparelhadas uma à outra. Ao emparelhar VNets criadas por meio de diferentes modelos de implantação, ambas as VNets devem existir na *mesma* assinatura. A capacidade de emparelhar VNets criadas por meio de diferentes modelos de implantação que existem em assinaturas *diferentes* está em versão de **visualização**. Leia o artigo [Criar um emparelhamento de rede virtual usando o Powershell](virtual-networks-create-vnetpeering-arm-ps.md) para obter mais detalhes.
* Embora a comunicação entre VMs em VNets emparelhadas não tenha restrição de largura de banda adicional, há uma largura de banda de rede máxima, dependendo do tamanho da VM que ainda é aplicável. Para saber mais sobre a largura de banda de rede máxima para diferentes tamanhos de VM, leia os artigos sobre tamanhos de VM do [Windows](../virtual-machines/windows/sizes.md) ou do [Linux](../virtual-machines/linux/sizes.md).

![Emparelhamento VNet básico](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividade
Depois que duas VNets são emparelhadas, funções de VMs ou Serviços de Nuvem na VNet podem se conectar diretamente a outros recursos conectados à rede virtual emparelhada. As duas VNets têm conectividade total no nível de IP.

A latência de rede para uma viagem de ida e volta entre duas VMs em VNets emparelhadas é a mesma que a de uma viagem de ida e volta em uma VNet única. A taxa de transferência da rede tem base na largura de banda permitida para a VM, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as VMs em VNets emparelhadas é roteado diretamente pela infraestrutura de back-end do Azure e não por meio de um gateway.

VMs conectadas a uma VNet podem acessar os pontos de extremidade de ILB (balanceamento de carga interno) na VNet emparelhada. Os NSGs (Grupos de Segurança da Rede) podem ser aplicados em qualquer VNet para bloquear o acesso a outra VNet ou sub-rede, se desejado.

Ao configurar o emparelhamento, você pode abrir ou fechar as regras de NSG entre as VNets. Se abrir conectividade total entre VNets emparelhadas (que é a opção padrão), você poderá aplicar NSGs a sub-redes específicas ou VMs para bloquear ou negar acesso específico. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.

A resolução de nomes DNS interna fornecida pelo Azure para VMs não funciona em VNets emparelhadas. As VMs têm nomes DNS internos que podem ser resolvidos somente na VNet virtual. No entanto, você pode configurar VMs conectadas a VNets emparelhadas como servidores DNS para uma VNet. Leia o artigo [Resolução de nome usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) para obter mais detalhes.

## <a name="service-chaining"></a>Encadeamento de serviços
Você pode configurar UDR (rotas definidas pelo usuário) que apontam para VMs em VNets emparelhadas como o endereço IP de "próximo salto", conforme mostrado no diagrama mais adiante neste artigo. Isso habilita o encadeamento de serviço, que permite direcionar o tráfego de uma VNet para um dispositivo virtual que está sendo executado em uma VNet emparelhada por meio de UDRs.

Você também pode criar efetivamente ambientes do tipo hub e spoke, nos quais o hub pode hospedar componentes de infraestrutura, como um dispositivo de rede virtual. Todas as VNets spoke que podem se emparelhar com ele, bem como um subconjunto de tráfego para dispositivos que estão em execução na VNet de hub. Em resumo, o emparelhamento de VNet permite que o próximo endereço IP no UDR seja o endereço IP de uma VM na VNet emparelhada. Leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md) para obter informações adicionais sobre UDRs.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conectividade local
Cada VNet, independentemente de ser emparelhada com outra VNet, ainda pode ter seu próprio gateway e usá-lo para se conectar a uma rede local. Os usuários também podem configurar a [conexão VNet para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) usando gateways, mesmo que as VNets estejam emparelhadas.

Quando as duas opções para a interconectividade da VNet estiverem configuradas, o tráfego entre as VNets fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as VNets estão emparelhadas, os usuários também podem configurar o gateway na VNet emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a VNet que está usando um gateway remoto não pode ter seu próprio gateway. Uma VNet pode ter apenas um gateway. O gateway pode ser um gateway local ou remoto (na VNet emparelhada), conforme mostrado na seguinte figura:

![Trânsito de emparelhamento VNet](./media/virtual-networks-peering-overview/figure02.png)

Não há suporte para o trânsito de gateway na relação de emparelhamento entre VNets criadas por meio de diferentes modelos de implantação. Ambas as VNets na relação de emparelhamento devem ter sido criadas pelo Gerenciador de Recursos para que um gateway de trânsito funcione.

Quando as VNets que compartilham uma única conexão do Azure ExpressRoute forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, pela rede de backbone do Azure). Você ainda pode usar gateways locais em cada VNet para se conectar ao circuito local. Como alternativa, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

## <a name="provisioning"></a>Provisionamento
O Emparelhamento VNet é uma operação privilegiada. É uma função separada sob o namespace da VirtualNetworks. Um usuário pode receber direitos específicos para autorizar o emparelhamento. Um usuário que tem acesso de leitura e gravação para a rede virtual herda automaticamente esses direitos.

Um usuário que é um administrador ou um usuário com privilégios da capacidade de emparelhamento pode iniciar uma operação de emparelhamento em outra VNet. Se houver uma solicitação correspondente para o emparelhamento no outro lado e outros requisitos forem atendidos, o emparelhamento será estabelecido.

Confira os artigos da seção [Próximas etapas](#next-steps) deste artigo para saber como estabelecer o emparelhamento de VNet entre duas VNet s.

## <a name="limits"></a>Limites
Há limites no número de emparelhamentos permitidos para uma única rede virtual. Confira os [Limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits) para saber mais.

## <a name="pricing"></a>Preços
Há um custo nominal para tráfego de entrada e saída que utiliza um emparelhamento de VNet. Para saber mais, consulte a [página de preço](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Próximas etapas
Aprenda a criar um emparelhamento de VNet usando:

* [O portal do Azure](virtual-networks-create-vnetpeering-arm-portal.md)
* [PowerShell do Azure](virtual-networks-create-vnetpeering-arm-ps.md)
* [Um modelo do Azure Resource Manager](virtual-networks-create-vnetpeering-arm-template-click.md)

