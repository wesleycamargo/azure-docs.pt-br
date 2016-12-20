---
title: "Como usar endereços IP públicos em uma rede virtual"
description: "Saiba como configurar uma rede virtual para usar endereços IP públicos"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: b6e5dd7c-84ea-491d-8314-88e63a4da108
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6ac3d099430267b33192899dbe9c9c35eb25b02e


---
# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espaço de endereço IP público em uma rede Virtual (VNet)
Redes virtuais (VNets) podem conter espaços de endereço IP públicos e privados (blocos de endereços RFC 1918). Quando você adiciona um intervalo de endereços IP públicos, ele será tratado como parte do espaço de endereço IP VNet particular que está acessível apenas através da VNet, VNets interconectadas e do seu local.

A figura abaixo mostra uma VNet que inclui espaços de endereço IP públicos e privados.

![IP público conceitual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Como posso adicionar um intervalo de endereços IP públicos?
Você adiciona um intervalo de endereços IP públicos da mesma maneira que adicionaria um intervalo de endereços IP privados: usando um arquivo *netcfg* ou adicionando a configuração no [portal do Azure](http://portal.azure.com). Você pode adicionar um intervalo de endereços IP públicos ao criar a sua VNet ou pode voltar e adicioná-lo posteriormente. O exemplo a seguir mostra espaço de endereços IP públicos e privados configurados na mesma VNet.

![Endereço IP público no Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Há alguma limitação?
Há alguns intervalos de endereços IP que não são permitidos:

* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Broadcast)
* 127.0.0.0/8 (loopback)
* 169.254.0.0/16 (link-local)
* 168.63.129.16/32 (DNS interno)

## <a name="next-steps"></a>Próximas etapas
[Como gerenciar servidores DNS usados por uma VNet](virtual-networks-manage-dns-in-vnet.md)




<!--HONumber=Nov16_HO3-->


