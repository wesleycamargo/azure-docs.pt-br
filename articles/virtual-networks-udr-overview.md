<properties 
   pageTitle="Visão geral sobre encaminhamento IP e rotas definidas pelo usuário"
   description="Visão geral sobre UDR (rotas definidas pelo usuário) e encaminhamento IP"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Encaminhamento IP e rotas definidas pelo usuário
O Azure usa uma tabela de rotas para decidir como encaminhar o tráfego IP com base no destino de cada pacote. Embora o Azure forneça uma tabela de rotas padrão com base em suas configurações de rede virtual, talvez seja necessário adicionar rotas personalizadas a essa tabela. A necessidade mais comum para uma entrada personalizada na tabela de rotas é o uso de um dispositivo virtual em seu ambiente do Azure. Leve em consideração o cenário mostrado na figura abaixo. Suponha que você queira garantir que todo o tráfego direcionado para a camada intermediária e sub-redes com suporte iniciado da sub-rede de front-end passe por um dispositivo de firewall virtual. Apenas adicionar o dispositivo à sua rede virtual e conectá-lo a diferentes sub-redes não fornecerá essa funcionalidade. Você também deve alterar a tabela de roteamento aplicada à sua sub-rede para garantir que os pacotes sejam encaminhados ao dispositivo de firewall virtual.

O mesmo ocorreria se você implementasse um dispositivo NAT virtual para controlar o tráfego entre sua rede virtual do Azure e a Internet. Para garantir que o dispositivo virtual seja usado, você precisa criar uma rota especificando que todo o tráfego destinado à Internet seja encaminhado ao dispositivo virtual.

## Roteamento
Os pacotes são roteados através de uma rede TCP/IP com base em uma tabela de rotas definida em cada nó na rede física. Uma tabela de rotas é uma coleção de rotas individuais usadas para decidir para onde encaminhar pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

- **Prefixo de endereço**. O CIDR de destino ao qual a rota se aplica, como 10.1.0.0/16.
- **Tipo do próximo salto**. O tipo de salto do Azure ao qual o pacote deve ser enviado. Os valores possíveis são:
	- **Local**. Representa a rede virtual local. Por exemplo, se você tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16, na mesma rede virtual, a rota para cada sub-rede na tabela de rotas terá um valor de próximo salto de *Local*.
	- **Gateway de VPN**. Representa um Gateway de VPN S2S do Azure. 
	- **Internet**. Representa o gateway de Internet padrão fornecido pela Infraestrutura do Azure 
	- **Dispositivo virtual**. Representa um dispositivo virtual que você adicionou à sua rede virtual do Azure.
	- **NULO**. Representa um buraco negro. Pacotes encaminhados a um buraco negro não serão encaminhados.
- **Valor de próximo salto**. O valor de próximo salto contém o endereço IP para o qual os pacotes devem ser encaminhados. Os valores de próximas salto são permitidos apenas em rotas em que o próximo salto é um *Dispositivo Virtual*.

## Rotas padrão
Cada sub-rede criada em uma rede virtual é automaticamente associada a uma tabela de rotas que contém as seguintes regras de rota padrão: - **regra de Vnet Local**: essa regra é criada automaticamente para todas as sub-redes em uma rede virtual. Ela especifica que há um vínculo direto entre as VMs na VNet e não há nenhum próximo salto intermediário. - **Regra local**: essa regra se aplica a todo o tráfego destinado ao intervalo de endereços locais e usa o gateway de VPN como o próximo salto de destino. - **Regra de Internet**: essa regra processa todo o tráfego destinado à Internet pública e usa o gateway de Internet de infraestrutura como o próximo salto para todo o tráfego destinado à Internet.

## Rotas BGP
Se houver uma conexão ExpressRoute entre sua rede local e o Azure, você poderá habilitar o BGP para propagar rotas da rede local para o Azure. Essas rotas BGP são usadas da mesma maneira que as rotas padrão e as rotas definidas pelo usuário em cada sub-rede do Azure. Para obter mais informações, consulte [Introdução ao ExpressRoute](../expressroute-introduction).

[AZURE.IMPORTANT]Você pode configurar seu ambiente do Azure para usar um túnel à força por meio de sua rede local, criando uma rota definida pelo usuário para a sub-rede 0.0.0.0/0 que usa o gateway de VPN como o próximo salto. No entanto, isso só funcionará se você estiver usando um gateway de VPN, não o ExpressRoute. Para o ExpressRoute, o túnel à força é configurado por meio do BGP.

## Rotas definidas pelo usuário
Não é possível exibir as rotas padrão especificadas acima em seu ambiente do Azure e, para a maioria dos ambientes, essas são as únicas rotas de que você precisará. No entanto, talvez seja necessário criar uma tabela de rotas e adicionar uma ou mais rotas em casos específicos, como:

- Túnel à força para a Internet através de sua rede local.
- Uso de dispositivos virtuais em seu ambiente do Azure.
- 
Nos cenários acima, você precisará criar uma tabela de rotas e adicionar rotas definidas pelo usuário a ela. Você pode ter várias tabelas de rotas, e a mesma tabela de rotas pode ser associada a uma ou mais sub-redes. Cada sub-rede só pode ser associada a uma única tabela de rotas. Todas as VMs e serviços em nuvem em uma sub-rede usam a tabela de rotas associada a essa sub-rede.

As sub-redes contam com rotas padrão até que uma tabela de rotas seja associada à sub-rede. Quando existe uma associação, o roteamento é feito com base em LPM (Correspondência de Prefixo mais Longo) entre as rotas definidas pelo usuário e as rotas padrão. Se houver mais de uma rota com a mesma correspondência LPM, uma rota será selecionada com base em sua origem na seguinte ordem:

1. Rota definida pelo usuário
1. Rota BGP (quando o ExpressRoute é usado)
1. Rota padrão

[AZURE.IMPORTANT]As rotas definidas pelo usuário são aplicadas apenas a VMs do Azure e a serviços de nuvem. Por exemplo, se desejar adicionar um dispositivo virtual de firewall entre sua rede local e o Azure, você terá que criar uma rota definida pelo usuário para as tabelas de rotas do Azure que encaminham todo o tráfego direcionado ao espaço de endereço local para o dispositivo virtual. No entanto, o tráfego de entrada do espaço de endereço local fluirá através de seu gateway de VPN ou circuito do ExpressRoute diretamente para o ambiente do Azure, ignorando o dispositivo virtual.

## Encaminhamento IP
Conforme descrito acima, uma das principais razões para criar uma rota definida pelo usuário é encaminhar o tráfego para um dispositivo virtual. Um dispositivo virtual é nada mais do que uma VM que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Essa VM de dispositivo virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você deve habilitar o Encaminhamento IP na VM.

## Consulte também

[Como criar rotas e habilitar o encaminhamento IP no Azure](../virtual-networks-udr-how-to)

[ILIP (IP público em nível de instância)](../virtual-networks-instance-level-public-ip)

[Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx)

<!--HONumber=52-->
