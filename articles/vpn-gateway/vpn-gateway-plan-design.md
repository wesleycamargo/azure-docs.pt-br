<properties 
   pageTitle="Planejamento e design de Gateway de VPN| Microsoft Azure"
   description="Saiba mais sobre o planejamento e design de Gateway de VPN para conexões entre locais, híbridas e VNet a VNet"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/20/2016"
   ms.author="cherylmc"/>

# Planejamento e design para o Gateway de VPN

Planejar e projetar suas conexões entre redes virtuais e entre locais pode ser muito simples ou bastante complicado, dependendo das suas necessidades de rede. Este artigo traz considerações básicas de design e planejamento.

## Planejamento


### <a name="compare"></a>1. Comparar as opções de conectividade entre locais

Se você decidiu que deseja conectar seus sites locais com segurança a uma rede virtual, há três maneiras diferentes para isso: Site a Site, Ponto a Site e Rota Expressa. Compare as diferentes conexões entre locais que estão disponíveis. A opção escolhida pode depender de várias considerações, como:


- Que tipo de taxa de transferência sua solução exige?
- Você deseja se comunicar através da Internet pública por meio de VPN segura ou através de uma conexão privada?
- Você tem um endereço IP público disponível para uso?
- Você planeja usar um dispositivo VPN? Nesse caso, é compatível?
- Você está conectando apenas alguns computadores ou deseja ter uma conexão persistente para o seu site?
- Qual tipo de gateway de VPN é necessário para a solução que você deseja criar?
- Qual SKU de gateway você deve usar?


A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>2. Considerar os requisitos de gateway por tipo de VPN e SKU


Ao criar um gateway de VPN, você precisará especificar a SKU de gateway que deseja usar. Há 3 SKUs de gateway de VPN:

- Básico
- Standard
- Alto Desempenho

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### 3\. Tipos de gateway e estimativas de taxa de transferência agregadas

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. A taxa de transferência agregada estimada pode ser um fator decisivo para seu design. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>4. Fluxo de trabalho

A lista a seguir descreve o fluxo de trabalho comum para conectividade de nuvem:

1.	Projete e planeje sua topologia de conectividade e liste os espaços de endereço para todas as redes às quais você deseja se conectar.
2.	Crie uma rede virtual do Azure. 
3.	Criar um gateway de VPN para a rede virtual.
4.	Crie e configure as conexões com redes locais ou outras redes virtuais (conforme necessário).
5.	Crie e configure uma conexão Ponto a Site para o gateway de VPN do Azure (conforme necessário).
 

## Design

### 1\. Selecionar uma topologia de conexão

Começar examinando o artigo [Topologias de conexão](vpn-gateway-topology.md). O artigo contém diagramas básicos, os modelos de implantação para cada topologia (Resource Manager ou clássica) e quais ferramentas de implantação você pode usar para implantar sua configuração.

### 2\. Entender as noções básicas de design

As seções abaixo discutem as noções básicas de gateway de VPN. Além disso, considere também as [Limitações dos serviços de rede](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Sobre sub-redes

Ao planejar e projetar a conexão que melhor funciona para seu ambiente, é muito importante considerar os intervalos de endereços IP e sub-redes que você tem disponíveis para uso.

Você precisará criar uma sub-rede de gateway para sua rede virtual a fim de configurar um gateway de VPN. Todas as sub-redes de gateway devem ser nomeadas como GatewaySubnet para funcionar adequadamente. Lembre-se de não pode nomear sua sub-rede de gateway com um nome diferente e não implantar VMs ou qualquer outra coisa para a sub-rede de gateway. Para obter mais informações sobre as sub-redes de gateway, consulte a seção [Sub-rede de gateway](vpn-gateway-about-vpngateways.md#gwsub) no artigo Sobre Gateways de VPN.

Ao criar conexões, em muitos casos é necessário tomar cuidado para não sobrepor os intervalos de endereços de sub-rede entre as conexões. Uma sub-rede sobreposta ocorre quando uma rede virtual ou local contém o mesmo espaço de endereço que outro local. Isso significa que seus engenheiros de rede para redes locais deverão definir um intervalo para usar o espaço/sub-redes de endereçamento IP do Azure. Você precisará de espaço de endereço que não está sendo usado na rede local.

Também é importante evitar a sobreposição de sub-redes quando você estiver trabalhando com conexões VNet a VNet. A criação de uma conexão VNet a VNet falhará se houver sobreposição nas suas sub-redes e um endereço IP existir em ambas as VNets de envio e de destino. Nesse caso, o Azure não poderá rotear os dados para a outra VNet porque o endereço de destino faz parte da VNet de envio.



#### <a name="local"></a>Sobre gateways de rede local

O gateway de rede local geralmente se refere ao seu local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local. Você dará ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especificará os prefixos de endereço que estão localizados no caminho local. O Azure examinará os prefixos de endereço de destino para ol tráfego de rede, conferirá a configuração que você especificou para o gateway de rede local e roteará os pacotes adequadamente. Você pode modificar os prefixos de endereço conforme necessário. Para obter mais informações sobre gateways de rede local, consulte a seção [Gateways de rede local](vpn-gateway-about-vpngateways.md#lng) no artigo Sobre Gateways de VPN.


#### <a name="gwtype"></a>Sobre tipos de gateway

É crucial selecionar o tipo de gateway correto para sua topologia. Seu gateway não funcionará corretamente se você selecionar o tipo incorreto. O tipo de gateway especifica como o próprio gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Gerenciador de Recursos.

Os tipos de gateway são:

- Vpn
- Rota Expressa

#### Sobre os tipos de conexão

Cada configuração exige um tipo específico de conexão. Os tipos de conexão são:

- IPsec
- Vnet2Vnet
- Rota Expressa
- VPNClient


#### <a name="vpntype"></a>Sobre os tipos de VPN

Cada configuração requer um tipo específico de VPN para funcionar. Se estiver combinando duas configurações, como a criação de uma conexão Site a Site e uma conexão Ponto a Site com a mesma rede virtual, você deverá usar um tipo VPN que atenda a ambos os requisitos de conexão. No caso de conexões coexistentes Ponto a Site e Site a Site, você deve usar um tipo de VPN baseada em rota ao trabalhar com o modelo de implantação do Azure Resource Manager ou um gateway dinâmico se estiver trabalhando com o modo de implantação clássico.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

As tabelas a seguir mostram o tipo VPN conforme mapeado para cada configuração de conexão. Verifique se o tipo de VPN para o gateway corresponde à configuração que você deseja criar.


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>3. Selecione um dispositivo VPN para conexões Site a Site

Para configurar uma conexão Site a Site, independentemente do modelo de implantação, você precisará os seguintes itens:

- Um dispositivo VPN que é compatível com os gateways de VPN do Azure
- Um endereço IP IPv4 público que não está protegido por um NAT

Você precisará ter experiência em configurar seu dispositivo VPN para criar uma configuração de Site a Site. Para saber mais sobre dispositivos VPN, consulte [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). O artigo de dispositivos VPN contém informações sobre dispositivos validados, requisitos para dispositivos que não foram validados e links para os documentos de configuração do dispositivo para cada dispositivo, se estiverem disponíveis.

### <a name="forcedtunnel"></a>4. Considere a possibilidade de roteamento de túnel forçado

Para a maioria das configurações, é possível configurar o túnel forçado. O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de Site a Site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais.

Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança. Para obter mais informações sobre como configurar o túnel forçado, consulte [Sobre túnel forçado para o modelo de implantação clássico](vpn-gateway-about-forced-tunneling.md) e [Sobre o túnel forçado para o modelo de implantação do Resource Manager](vpn-gateway-about-forced-tunneling.md).

**Diagrama de túnel forçado**

![Conexão de túnel forçado](./media/vpn-gateway-plan-design/forced-tunnel.png "túnel forçado")


Esta tabela lista o modelo de implantação para o qual o túnel forçado está disponível e as ferramentas de implantação que você pode usar para configurar o túnel forçado e será vinculado diretamente a um artigo, se disponível. Atualizamos as tabelas com frequência, à medida que novos artigos ficam disponíveis para uso.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## Próximas etapas

Consulte [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) e [Sobre Gateways de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações para ajudá-lo com seu design. Para obter mais informações sobre topologias de conexão, consulte [Topologias de conexão](vpn-gateway-topology.md).

<!---HONumber=AcomDC_0504_2016-->