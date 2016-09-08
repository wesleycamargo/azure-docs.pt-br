<properties 
   pageTitle="Sobre o Gateway de VPN| Microsoft Azure"
   description="Saiba mais sobre conexões de Gateway de VPN para Redes Virtuais do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Sobre o Gateway de VPN


O Gateway de VPN é uma coleção de recursos usados para enviar o tráfego de rede entre as redes virtuais e as localizações. Gateways são usados para as conexões de Site para Site, Ponto para Site e da Rota Expressa. O Gateway de VPN também é usado para enviar o tráfego entre várias redes virtuais no Azure (VNet para VNet).

Para criar uma conexão, você adicionar o gateway de rede virtual a uma rede virtual e define recursos adicionais do Gateway de VPN e suas configurações. Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter um gateway de rede virtual que usa - GatewayType Vpn, e outro que usa -GatewayType Rota Expressa.

Para saber mais sobre os requisitos do gateway, consulte [Requisitos de Gateway](vpn-gateway-about-vpn-gateway-settings.md#requirements). Para saber a taxa de transferência agregada estimada, consulte [Sobre Configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput). Para conhecer os preços, consulte [Preços de Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway). Para conhece as assinaturas e os limites de serviço, confira [Limites de Rede](../articles/azure-subscription-service-limits.md#networking-limits).

Quando você configura o Gateway de VPN, as instruções usadas dependem do modelo de implantação utilizado para criar a rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Confira [Noções básicas sobre o Gerenciador de Recursos e modelos de implantação clássicos](../resource-manager-deployment-model.md) para obter mais informações.

As seções a seguir contêm tabelas que listam as informações a seguir para a configuração:

- modelo de implantação disponível
- ferramentas de configuração disponíveis
- links que levam você diretamente a um artigo, se disponível


Use os diagramas e as descrições para ajudar a selecionar a topologia de configuração adequada a seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar topologias mais complexas usando os diagramas como uma diretriz. Cada configuração se baseia nas configurações de Gateway de VPN que você selecionar.

### Definição das configurações do Gateway de VPN

Como Gateway de VPN é um conjunto de recursos, você pode configurar alguns dos recursos usando uma ferramenta e, em seguida, alternar para outro a fim de definir configurações de recurso diferentes. Atualmente, é possível definir todas as configurações de recurso de Gateway de VPN no Portal do Azure. As instruções nos artigos para cada configuração especificam se uma ferramenta específica é necessária. Se você estiver trabalhando com o modelo de implantação clássico, talvez queira trabalhar no portal clássico ou usar o PowerShell nesse momento. Para saber mais sobre as configurações individuais disponíveis, veja [Sobre as configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).



## Site a Site e Vários Sites

### Site a site

Uma conexão Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (S2S ou IKEv1). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.

![Conexão S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### Multissite

Você pode criar e configurar uma conexão de VPN entre sua Rede Virtual e várias redes locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (gateway dinâmico para Redes Virtuais clássicas). Como uma Rede Virtual pode ter um gateway de rede virtual, todas as conexões por meio do gateway compartilham a largura de banda disponível. Esse tipo de configuração é frequentemente chamado de conexão de "vários sites".
 

![Conexão de vários sites](./media/vpn-gateway-about-vpngateways/demomulti.png "vários sites")

### Modelos e métodos de implantação

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet a VNet

Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN do Azure para fornecer um túnel seguro usando IPsec/IKE. Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

As redes virtuais às quais você se conecta podem estar:

- na mesma região ou em regiões diferentes
- na mesma assinatura ou em assinaturas diferentes
- no mesmo modelo de implantação ou em modelos de implantação diferentes



![Conectando VNet a VNet](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet a vnet")



### Conexões entre os modelos de implantação

Atualmente, o Azure tem dois modelos de implantação: o clássico e o Resource Manager. Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Você pode criar uma conexão entre as Redes Virtuais para permitir que os recursos em uma rede virtual se comuniquem diretamente com os recursos em outra.


### Modelos e métodos de implantação

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

### Emparelhamento VNet

Você poderá usar o emparelhamento de Rede Virtual para criar sua conexão, desde que a configuração da rede virtual atenda a determinados requisitos. O emparelhamento de Rede Virtual não usa um gateway de rede virtual. O [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) está em visualização no momento.



## Ponto a Site

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual para uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). As conexões P2S não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Você estabelece a conexão VPN ao iniciá-la do computador cliente. Essa é uma solução útil quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma Rede Virtual.


![Conexão ponto a site](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### Modelos e métodos de implantação

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## Rota Expressa

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md).


## Conexões coexistentes Site a Site e de Rota Expressa

A Rota Expressa é uma conexão direta e dedicada da sua WAN (não pela Internet pública) para Serviços Microsoft, incluindo o Azure. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e Rota Expressa para a mesma rede virtual oferece várias vantagens.

Você pode configurar uma VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio da Rota Expressa. Essa configuração exige dois gateways de rede virtual para a mesma rede virtual, um usando -GatewayType Vpn e o outro usando -GatewayType Rota Expressa.


![Coexistência de conexão](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### Modelos e métodos de implantação

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Próximas etapas

Veja as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para saber mais sobre Gateways de VPN

Conecte seu local a uma Rede Virtual. Veja [Criar uma conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 

<!---HONumber=AcomDC_0831_2016-->