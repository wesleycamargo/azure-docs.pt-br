Você deve criar uma rede virtual e uma sub-rede de gateway primeiro, antes de iniciar as tarefas a seguir. Confira o artigo [Configurar uma Rede Virtual usando o portal clássico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obter mais informações.

## Adicionar um gateway

Use o comando a seguir para criar um gateway. Certifique-se de substituir quaisquer valores pelos seus próprios.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## Verificar se o gateway foi criado

Use o comando abaixo para verificar se o gateway foi criado. Esse comando também recupera a ID do gateway, que você precisa para outras operações.

	Get-AzureVirtualNetworkGateway

## Redimensionar um gateway

Há três [SKUs de gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## Remover um gateway

Use o comando a seguir para remover um gateway

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->