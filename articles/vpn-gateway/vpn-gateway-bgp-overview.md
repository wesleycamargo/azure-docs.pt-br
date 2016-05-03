<properties
   pageTitle="Visão geral de BGP com Gateways de VPN do Azure | Microsoft Azure"
   description="Este artigo fornece uma visão geral do BGP com Gateways de VPN do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/26/2016"
   ms.author="yushwang"/>

# Visão geral de BGP com Gateways de VPN do Azure

Este artigo fornece uma visão geral do suporte a BGP (Border Gateway Protocol) em Gateways de VPN do Azure.

## Sobre BGP

BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. Quando usado no contexto de Redes Virtuais do Azure, o BGP habilita os Gateways de VPN do Azure e os dispositivos de VPN local, chamados de pares de BGP ou vizinhos, a trocar "rotas" que informarão ambos os gateways da disponibilidade e da acessibilidade para que esses prefixos percorram os gateways ou os roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway BGP obtém de um par de BGP para todos os outros pares de BGP.
 
### Por que usar o BGP?

O BGP é um recurso opcional que você pode usar com gateways de VPN Baseados em Rota do Azure. Você também deve verificar se os dispositivos de VPN locais dão suporte a BGP antes de habilitar o recurso. Você pode continuar a usar gateways de VPN do Azure e seus dispositivos de VPN locais sem BGP. É o equivalente ao uso de rotas estáticas (sem BGP) *versus* o uso de roteamento dinâmico com BGP entre suas redes e o Azure.

Há várias vantagens e novos recursos com o BGP:

#### Suporte a atualizações prefixo flexíveis e automáticas

Com o BGP, você só precisa declarar um prefixo mínimo para um par de BGP específico através do túnel de VPN S2S IPsec. Ele pode ser tão pequeno quanto um prefixo de host (/32) do endereço IP do par de BGP do dispositivo VPN local. Você pode controlar quais prefixos de rede locais deseja anunciar ao Azure para permitir que sua Rede Virtual do Azure os acesse.
	
Você também pode anunciar prefixos maiores que podem incluir alguns de seus prefixos de endereço de VNet, como a rota padrão (0.0.0.0/0) ou um espaço de endereço IP privado grande (por exemplo, 10.0.0.0/8). Observe que os prefixos não podem ser idênticos a nenhum de seus prefixos de VNet. As rotas idênticas aos prefixos de VNet serão rejeitadas.

#### Suporte a vários túneis entre uma VNet e um site local com failover automático com base em BGP

Você pode estabelecer várias conexões entre sua VNet do Azure e os dispositivos de VPN locais no mesmo local. Esse recurso fornece vários túneis (caminhos) entre as duas redes em uma configuração ativo-ativo. Se um dos túneis for desconectado, as rotas correspondentes serão retiradas por meio do BGP, e o tráfego mudará automaticamente para os túneis restantes.
	
O seguinte diagrama mostra um exemplo simples dessa configuração altamente disponível:
	
![Vários caminhos ativos](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### Suporte ao roteamento de trânsito entre as redes locais e várias VNets do Azure

O BGP habilita vários gateways a aprender e propagar prefixos de redes diferentes, quer elas estejam conectadas direta ou indiretamente. Isso pode habilitar o roteamento de tráfego com gateways de VPN do Azure entre os sites locais ou em várias Redes Virtuais do Azure.
	
O seguinte diagrama mostra um exemplo de uma topologia de vários saltos com vários caminhos que podem transportar o tráfego entre as duas redes locais por meio de gateways de VPN do Azure dentro das Redes da Microsoft:

![Trânsito com vários saltos](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## Perguntas frequentes sobre o BGP

#### O BGP tem suporte em todas as SKUs de Gateway de VPN do Azure?

Não, o BGP tem suporte nos gateways de VPN **Standard** e **HighPerformance** do Azure. A SKU **Basic** não tem suporte.

#### Posso usar o BGP com gateways de VPN Baseados em Política do Azure?

Não, há suporte ao o BGP somente em gateways de VPN Baseados em Rota.

#### Posso usar ASNs (Números de Sistema Autônomo) privados?

Sim, você pode usar seu próprio ASNs públicos ou privados para suas redes locais e para redes virtuais do Azure.

#### Posso usar o mesmo ASN para redes de VPN locais e VNets do Azure?

Não, você deverá atribuir ASNs diferentes entre suas redes locais e as VNets do Azure se os estiver conectando junto com o BGP. Os Gateways de VPN do Azure têm um ASN padrão de 65515 atribuído, quer o BGP esteja habilitado ou não para a conectividade entre locais. Você pode substituir esse padrão atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois de criar o gateway. Você precisará atribuir ASNs locais aos Gateways de Rede Local do Azure correspondentes.



#### Quais prefixos de endereço os gateways de VPN do Azure anunciarão para mim?

O Gateway de VPN do Azure anunciará as seguintes rotas para seus dispositivos de BGP locais:

- Seus prefixos de endereços de VNet
- Prefixos de endereços para cada Gateway de Rede Local conectado ao gateway de VPN do Azure
- As rotas obtidas de outras sessões de emparelhamento de BGP conectadas ao gateway de VPN do Azure, **exceto rotas padrão sobrepostas com qualquer prefixo de VNet**.

#### Posso usar o BGP com minhas conexões VNet para VNet?

Sim, você pode usar o BGP para conexões entre locais e conexões de VNet para VNet.

#### Posso combinar o BGP a conexões não BGP para meu gateways de VPN do Azure?

Sim, você pode combinar conexões BGP e não BGP para o mesmo gateway de VPN do Azure.

#### O gateway de VPN do Azure dá suporte ao roteamento de trânsito de BGP?

Sim, o roteamento de trânsito de BGP tem suporte, com a exceção de que os gateways de VPN do Azure **NÃO** anunciarão rotas padrão para outros pares de BGP. Para habilitar o roteamento de trânsito entre vários gateways de VPN do Azure, você deve habilitar o BGP em todas as conexões de VNet para VNet intermediárias.

#### Posso ter mais de um túnel entre meu gateway de VPN do Azure e minha rede local?

Sim, você pode estabelecer mais de um túnel de VPN S2S entre um gateway de VPN do Azure e sua rede local. Observe que todos esses túneis serão contados em relação ao número total de túneis para seus gateways de VPN do Azure. Por exemplo, se você tiver dois túneis redundantes entre o gateway de VPN do Azure e uma de suas redes locais, elas consumirão dois túneis da cota total de seu gateway de VPN do Azure (10 para Standard e 30 para HighPerformance).

#### Posso ter vários túneis entre duas VNets do Azure com o BGP?

Não, não há suporte a túneis redundantes entre um par de redes virtuais.

#### Que endereço o gateway de VPN do Azure usa para o IP de Par de BGP?

O gateway de VPN do Azure alocará um único endereço IP do intervalo de GatewaySubnet definido para a rede virtual. Por padrão, ele é o penúltimo endereço do intervalo. Por exemplo, se o GatewaySubnet for 10.12.255.0.0/27, que varia de 10.42.255.0.0 a 10.42.255.31, o endereço IP do Par de BGP no gateway de VPN do Azure será 10.12.255.30. Você pode localizar essas informações ao listar as informações de gateway de VPN do Azure.

#### Quais são os requisitos para os endereços IP de Par de BGP em meu dispositivo VPN?

Seu endereço de par do BGP local **NÃO DEVE** ser o mesmo que o endereço IP público do dispositivo VPN. Use um endereço IP diferente no dispositivo VPN para o IP de Par de BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. Especifique esse endereço no Gateway de Rede Local correspondente que representa o local.

#### O que devo especificar como meus prefixos de endereço para o Gateway de Rede Local ao usar o BGP?

O Gateway de Rede Local do Azure especifica os prefixos de endereços iniciais para a rede local. Com o BGP, você deve alocar o prefixo de host (prefixo /32) de seu endereço IP de Par de BGP como o espaço de endereço da rede local. Se o IP de Par de BGP for 10.52.255.254, você deverá especificar "10.52.255.254/32" como localNetworkAddressSpace do Gateway de Rede Local que representa essa rede local. Isso é para garantir que o gateway de VPN do Azure estabeleça a sessão de BGP através do túnel de VPN S2S.

#### O que devo adicionar a meu dispositivo VPN local para a sessão de emparelhamento de BGP?

Você deve adicionar uma rota de host do endereço IP de Par de BGP do Azure em seu dispositivo VPN apontando para o túnel de VPN S2S IPsec. Por exemplo, se o IP de Par de VPN do Azure for "10.12.255.30", você deverá adicionar uma rota de host para "10.12.255.30" com uma interface de nexthop da interface de túnel IPsec correspondente em seu dispositivo VPN.

## Próximas etapas

Confira [Introdução ao BGP em gateways de VPN do Azure](./vpn-gateway-bgp-resource-manager-ps.md) para obter as etapas de configuração do BGP para suas conexões entre locais e VNet para VNet.

<!---HONumber=AcomDC_0427_2016-->