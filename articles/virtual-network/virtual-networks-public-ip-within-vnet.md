<properties 
   pageTitle="Como usar endereços IP públicos em uma rede virtual"
   description="Saiba como configurar uma rede virtual para usar endereços IP públicos"
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
   ms.date="06/08/2015"
   ms.author="telmos" />

# Espaço de endereço IP público em uma rede Virtual (VNet)

Agora você pode adicionar espaço de endereço IP público a seus VNets. Antes, você somente podia adicionar blocos de endereços RFC 1918 (espaço privado) a seus VNets. Quando você adiciona um intervalo de endereços IP públicos, ele será tratado como parte do espaço de endereço IP VNet particular que está acessível apenas através da VNet, VNets interconectadas e do seu local.

A adição de um espaço de endereço IP público funciona assim na teoria:

![IP público conceitual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Como posso adicionar um intervalo de endereços IP públicos?

Você adiciona um intervalo de endereços IP públicos da mesma maneira que adicionaria um intervalo de endereços IP privados: usando um arquivo *netcfg* ou fazendo a configuração no portal. Você pode adicionar um intervalo de endereços IP públicos ao criar a sua VNet ou pode voltar e adicioná-lo posteriormente. O exemplo a seguir mostra espaço de endereços IP públicos e privados configurados na mesma rede virtual.

![Endereço IP público no Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Há alguma limitação?

Há alguns intervalos de endereços IP que não são permitidos:

- 224\.0.0.0/4 (Multicast)

- 255\.255.255.255/32 (Broadcast)

- 127\.0.0.0/8 (loopback)

- 169\.254.0.0/16 (link-local)

- 68\.63.129.16/32 (DNS interno)

## Próximas etapas

[Como gerenciar as propriedades da rede virtual (VNet)](../virtual-networks-settings)

[Como gerenciar servidores DNS usados por uma rede virtual (VNet)](../virtual-networks-manage-dns-in-vnet)

[Como excluir uma rede virtual (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=August15_HO6-->