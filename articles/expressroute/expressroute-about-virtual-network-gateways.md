<properties 
   pageTitle="Sobre gateways de rede virtual ExpressRoute | Microsoft Azure"
   description="Saiba mais sobre os gateways de rede virtual para ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />


# <a name="about-virtual-network-gateways-for-expressroute"></a>Sobre os gateways de rede virtual para ExpressRoute


O gateway de rede virtual é usado para enviar o tráfego de rede entre as redes virtuais do Azure e locais. Quando você configura uma conexão ExpressRoute, é necessário criar e configurar um gateway de rede virtual e uma conexão de gateway de rede virtual.

Quando você cria um gateway de rede virtual, especifica várias configurações. Uma das configurações necessárias especifica se o gateway será usado para tráfego de ExpressRoute ou Tráfego VPN Site a Site. No modelo de implantação do Resource Manager, a configuração é '-GatewayType'.

Quando o tráfego de rede é enviado em uma conexão privada dedicada, você pode usar o tipo de gateway 'ExpressRoute'. Isso também é referido como um gateway ExpressRoute. Quando o tráfego de rede é enviado criptografado na Internet pública, você pode usar o tipo de gateway 'Vpn'. Isso é referido como um gateway VPN. As conexões Site a Site, Ponto a Site e VNet a VNet usam um gateway VPN. 

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter um gateway de rede virtual que usa - GatewayType Vpn, e outro que usa -GatewayType Rota Expressa. Este artigo se concentra no gateway de rede virtual ExpressRoute.

## <a name="<a-name="gwsku"></a>gateway-skus"></a><a name="gwsku"></a>SKUs do Gateway

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se quiser atualizar o gateway para um SKU de gateway mais potente, na maioria dos casos, você pode usar o cmdlet do PowerShell 'Resize-AzureRmVirtualNetworkGateway'. Isso funcionará em atualizações para as SKUs Standard e HighPerformance. No entanto, para atualizar para a SKU UltraPerformance, você precisará recriar o gateway.

###  <a name="<a-name="aggthroughput"></a>estimated-aggregate-throughput-by-gateway-sku"></a><a name="aggthroughput"></a>Taxa de transferência agregada estimada por SKU de gateway


A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="<a-name="resources"></a>rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>Cmdlets do PowerShell e APIs REST

Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar cmdlets do PowerShell e APIs REST para configurações do gateway de rede virtual, veja as páginas a seguir:

|**Clássico** | **Gerenciador de Recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Próximas etapas

Consulte [Visão geral de ExpressRoute](expressroute-introduction.md) para saber mais sobre as configurações de conexão disponíveis. 







 



<!--HONumber=Oct16_HO2-->


