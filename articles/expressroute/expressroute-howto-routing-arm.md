<properties
   pageTitle="Como configurar o roteamento de um circuito da Rota Expressa | Microsoft Azure"
   description="Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento do emparelhamento público, privado e da Microsoft de um circuito de Rota Expressa. Este artigo também mostra como verificar o status, atualizar ou excluir emparelhamentos de seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/04/2015"
   ms.author="ganesr"/>

# Criar e modificar a configuração de roteamento da Rota Expressa

> [AZURE.SELECTOR]
[PowerShell Classic](expressroute-howto-routing-classic.md)
[PowerShell Resource Manager](expressroute-howto-routing-arm.md)

Este artigo explica as etapas para criar e gerenciar a configuração de roteamento de um circuito da Rota Expressa usando cmdlets do PowerShell e o modelo de implantação ARM. As etapas a seguir também mostrarão a você como verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito da Rota Expressa.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)] 


## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell, versão 1.0 ou posterior.
- Leia as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de roteamento](expressroute-routing.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito da Rota Expressa ativo. Antes de continuar, siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito da Rota Expressa deve estar em um estado provisionado e habilitado e para que você possa executar os cmdlets descritos abaixo.

>[AZURE.IMPORTANT]Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. Nesses casos, não será possível criar ou gerenciar emparelhamentos.

Você pode configurar um, dois ou todos os três emparelhamentos (privado e público do Azure e da Microsoft) para um circuito da Rota Expressa. Você pode configurar emparelhamentos em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez.

## Emparelhamento privado do Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito da Rota Expressa.

### Criar um emparelhamento privado do Azure

1. **Importe o módulo do PowerShell para Rota Expressa.**
	
 	Você deve instalar o instalador do Powershell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets da Rota Expressa. Você precisará executar o PowerShell como Administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecido

		Import-AzureRM

	Você também pode importar apenas um módulo selecionado dentro do intervalo de versão semântico conhecido
		
		Import-Module AzureRM.Network 

	Fazer logon em sua conta

		Login-AzureRmAccount

	Selecionar a assinatura na qual você deseja criar um circuito da Rota Expressa
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Criar um circuito da Rota Expressa.**
	
	Siga as instruções para criar um [circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de Camada 3, peça que ele habilite o emparelhamento privado do Azure para você. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.

3. **Verifique se o circuito da Rota Expressa foi provisionado.**

	Primeiro, verifique se o circuito da Rota Expressa está Provisionado e Habilitado. Veja o exemplo abaixo.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	A resposta será algo semelhante ao exemplo a seguir:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


4. **Configure o emparelhamento privado do Azure para o circuito.**

	Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:

	- Uma sub-rede /30 para o link principal. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
	- Uma sub-rede /30 para o link secundário. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
	- Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
	- Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Não use 65515.
	- Hash MD5, se você optar por usar um. **Isso é opcional**.
	
	Você pode executar o seguinte cmdlet para configurar o emparelhamento privado do Azure para seu circuito.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Use o cmdlet abaixo se você optar por usar um hash MD5.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	>[AZURE.IMPORTANT]Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.

### Obter detalhes sobre o emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt	


### Atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir. No exemplo abaixo, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

	Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Excluir um emparelhamento privado do Azure


Você pode remover a configuração de emparelhamento executando o seguinte cmdlet.

>[AZURE.IMPORTANT]Verifique se todas as redes virtuais estão desvinculadas do circuito da Rota Expressa antes de executar esse cmdlet.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Emparelhamento público do Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito da Rota Expressa.

### Criar o emparelhamento público do Azure

1. **Importe o módulo do PowerShell para Rota Expressa.**
	
 	Você deve instalar o instalador do Powershell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets da Rota Expressa. Você precisará executar o PowerShell como Administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecido

		Import-AzureRM

	Você também pode importar apenas um módulo selecionado dentro do intervalo de versão semântico conhecido
		
		Import-Module AzureRM.Network 

	Fazer logon em sua conta

		Login-AzureRmAccount

	Selecionar a assinatura na qual você deseja criar um circuito da Rota Expressa
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Criar um circuito da Rota Expressa**
	
	Siga as instruções para criar um [circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de Camada 3, peça que ele habilite o emparelhamento privado do Azure para você. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.

3. **Verifique se o circuito da Rota Expressa foi provisionado**

	Primeiro, verifique se o circuito da Rota Expressa está Provisionado e Habilitado. Veja o exemplo abaixo.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	A resposta será algo semelhante ao exemplo a seguir:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	

4. **Configure o emparelhamento público do Azure para o circuito**

	Tenha as informações a seguir antes de prosseguir:

	- Uma sub-rede /30 para o link principal. Precisa ser um prefixo IPv4 público válido.
	- Uma sub-rede /30 para o link secundário. Precisa ser um prefixo IPv4 público válido.
	- Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
	- Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você deve usar um número de AS público para esse emparelhamento.
	- Hash MD5, se você optar por usar um. **Isso é opcional**.
	
	Você pode executar o seguinte cmdlet para configurar o emparelhamento privado do Azure para seu circuito

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Use o cmdlet abaixo se você optar por usar um hash MD5

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


	>[AZURE.IMPORTANT]Especifique o número de AS como um ASN de emparelhamento e não um ASN de cliente.

### Obter detalhes sobre o emparelhamento público do Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir

	Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

No exemplo acima, a ID de VLAN do circuito está sendo atualizada de 200 para 600.

### Excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Emparelhamento da Microsoft

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito da Rota Expressa.

### Criar emparelhamento da Microsoft

1. **Importe o módulo do PowerShell para Rota Expressa.**
	
 	Você deve instalar o instalador do Powershell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importar os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets da Rota Expressa. Você precisará executar o PowerShell como Administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importar todos os módulos AzureRM.* dentro do intervalo de versão semântico conhecido

		Import-AzureRM

	Você também pode importar apenas um módulo selecionado dentro do intervalo de versão semântico conhecido
		
		Import-Module AzureRM.Network 

	Fazer logon em sua conta

		Login-AzureRmAccount

	Selecionar a assinatura na qual você deseja criar um circuito da Rota Expressa
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Criar um circuito da Rota Expressa**
	
	Siga as instruções para criar um [circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de Camada 3, peça que ele habilite o emparelhamento privado do Azure para você. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.

3. **Verifique se o circuito da Rota Expressa foi provisionado**

	Primeiro, verifique se o circuito da Rota Expressa está Provisionado e Habilitado. Veja o exemplo abaixo.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	A resposta será algo semelhante ao exemplo a seguir:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	
4. **Configurar o emparelhamento da Microsoft para o circuito**

	Você precisa ter as seguintes informações antes de continuar:

	- Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
	- Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
	- Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
	- Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você deve usar somente números AS públicos. Você deve ser proprietário do número de AS.
	- Prefixos anunciados: forneça uma lista com todos os prefixos que você pretende anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, envie uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
	- ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados. **Isso é opcional**.
	- Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
	- Um Hash MD5, se você optar por usar um. **Isso é opcional.**
	
	Execute o cmdlet a seguir para configurar o emparelhamento da Microsoft para seu circuito

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Obter detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o cmdlet a seguir.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### Atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir.

		Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
		

### Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Próximas etapas

Depois, Vincular uma Rede Virtual a um circuito da Rota Expressa. Você pode usar [esse modelo](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) ao trabalhar com o modo de implantação do Gerenciador de recursos do Azure. Atualmente estamos trabalhando em etapas do PowerShell.


-  Em seguida, [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md).

-  Para obter mais informações sobre fluxos de trabalho, veja [Fluxos de trabalho da Rota Expressa](expressroute-workflows.md).

-  Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md).

<!------HONumber=Nov15_HO4-->