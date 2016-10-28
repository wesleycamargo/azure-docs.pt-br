<properties 
   pageTitle="Como usar endereços IP públicos em uma rede virtual"
   description="Saiba como configurar uma rede virtual para usar endereços IP públicos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# Espaço de endereço IP público em uma rede Virtual (VNet)

Redes virtuais (VNets) podem conter espaços de endereço IP públicos e privados (blocos de endereços RFC 1918). Quando você adiciona um intervalo de endereços IP públicos, ele será tratado como parte do espaço de endereço IP VNet particular que está acessível apenas através da VNet, VNets interconectadas e do seu local.

A figura abaixo mostra uma VNet que inclui espaços de endereço IP públicos e privados.

![IP público conceitual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Como posso adicionar um intervalo de endereços IP públicos?

Você adiciona um intervalo de endereços IP públicos da mesma maneira que adicionaria um intervalo de endereços IP privados: usando um arquivo *netcfg* ou adicionando a configuração no [portal do Azure](http://portal.azure.com). Você pode adicionar um intervalo de endereços IP públicos ao criar a sua VNet ou pode voltar e adicioná-lo posteriormente. O exemplo a seguir mostra espaço de endereços IP públicos e privados configurados na mesma VNet.

![Endereço IP público no Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Há alguma limitação?

Há alguns intervalos de endereços IP que não são permitidos:

- 224\.0.0.0/4 (Multicast)

- 255\.255.255.255/32 (Broadcast)

- 127\.0.0.0/8 (loopback)

- 169\.254.0.0/16 (link-local)

- 168\.63.129.16/32 (DNS interno)

## Próximas etapas

[Como gerenciar servidores DNS usados por uma VNet](virtual-networks-manage-dns-in-vnet.md)

<!---HONumber=AcomDC_0810_2016-->