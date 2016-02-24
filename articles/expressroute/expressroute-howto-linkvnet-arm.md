<properties 
   pageTitle="Vinculando redes virtuais a circuitos da Rota Expressa | Microsoft Azure"
   description="Este documento apresenta uma visão geral de como vincular redes virtuais (VNets) a circuitos da Rota Expressa."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="ganesr" />

# Vinculando Redes Virtuais a circuitos da Rota Expressa

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

Este artigo apresenta uma visão geral de como vincular redes virtuais a circuitos da Rota Expressa. As redes virtuais podem estar na mesma assinatura ou fazerem parte de outra assinatura. Este artigo aplica-se a Redes Virtuais implantadas com o modelo de implantação do Gerenciador de Recursos. Se você quiser vincular uma rede virtual que foi implantada usando o modelo de implantação clássico, consulte [Vincular uma rede virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-classic.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell, versão 1.0 ou posterior. 
- Assegure-se de que você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos de roteamento](expressroute-routing.md) e a página de [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito da Rota Expressa ativo. 
	- Siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
	- Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções sobre roteamento. 
	- O emparelhamento privado do Azure deve estar configurado e o emparelhamento BGP entre a rede e a Microsoft deve estar em atividade para que você habilite a conectividade de ponta a ponta.
	- É necessário ter uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para criar um [gateway de VPN](../articles/vpn-gateway-create-site-to-site-rm-powershell.md)

Você pode vincular até 10 redes virtuais a um circuito da Rota Expressa. Todos os circuitos da Rota Expressa devem estar na mesma região geopolítica. É possível vincular um grande número de redes virtuais ao circuito da Rota Expressa se você tiver habilitado o complemento premium da Rota Expressa. Confira as [Perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## Conectando uma rede virtual na mesma assinatura do Azure a um circuito da Rota Expressa

Você pode vincular um gateway de rede virtual a um circuito da Rota Expressa usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o cmdlet.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Conectando uma rede virtual em uma assinatura diferente do Azure a um circuito da Rota Expressa

Um circuito da Rota Expressa pode ser compartilhado entre várias assinaturas. A figura abaixo mostra um esquema simples de como funciona o compartilhamento de circuitos da Rota Expressa entre várias assinaturas. Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito da Rota Expressa para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: TI) pode ter o circuito da Rota Expressa. Outras assinaturas dentro da organização podem usar o circuito de Rota Expressa.

>[AZURE.NOTE] As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito da Rota Expressa. Todas as redes virtuais compartilham a mesma largura de banda.

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administração

O *proprietário do circuito* é um usuário avançado autorizado do recurso de circuito da Rota Expressa. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos *usuários do circuito*. *Usuários do circuito* são proprietários de gateways de rede virtual (que não estão na mesma assinatura que o circuito da Rota Expressa). Os *usuários do circuito* podem resgatar autorizações (uma autorização por rede virtual).

O *proprietário do circuito* tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização fará com que todas as conexões de links sejam excluídos da assinatura cujo acesso foi revogado.

### Operações do proprietário do circuito 

#### Criando uma autorização
	
O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito da Rota Expressa. Uma autorização é válida apenas para uma conexão.

O trecho de cmdlet abaixo mostra como criar uma autorização.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

A resposta para isso conterá a chave de autorização e o status

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Examinando autorizações

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o cmdlet a seguir.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Adicionando autorizações

O proprietário do circuito pode adicionar autorizações usando o cmdlet a seguir.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Excluindo autorizações

O proprietário do circuito pode revogar/excluir autorizações usando o cmdlet a seguir.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Operações do usuário do circuito

O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de circuito é semelhante à listada abaixo:


A chave de autorização é um GUID.

#### Resgatando autorizações de conexão

O usuário de circuito pode executar o cmdlet a seguir para resgatar uma autorização de vínculo.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Liberando autorizações de conexão

É possível liberar uma autorização excluindo a conexão que vincula o circuito da Rota Expressa à rede virtual.

## Próximas etapas

Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=AcomDC_0128_2016-->