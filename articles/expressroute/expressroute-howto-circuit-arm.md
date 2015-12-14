<properties
   pageTitle="Criar um circuito da Rota Expressa usando o Gerenciador de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento de um circuito da Rota Expressa. Este artigo também mostra como verificar o status, atualizar ou excluir e desprovisionar seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/06/2015"
   ms.author="cherylmc"/>

# Criar e modificar um circuito da Rota Expressa usando o Gerenciador de recursos do Azure e PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Este artigo fornece uma orientação pelas etapas de criação de um circuito da Rota Expressa usando cmdlets do PowerShell e o modelo de implantação do Gerenciador de Recursos do Azure. As etapas a seguir também mostrarão a você como verificar o status, atualizar ou excluir e desprovisionar um circuito da Rota Expressa.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell, versão 1.0 ou posterior. Siga as instruções na página [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter orientação detalhada sobre como configurar o computador a fim de usar os módulos do Azure PowerShell. 
- Examine a página [Pré-requisitos](expressroute-prerequisites.md) e a página [Fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## Criar e provisionar um circuito da Rota Expressa

1. **Importe o módulo do PowerShell para Rota Expressa.**

 	Instale o instalador do PowerShell mais recente da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets da Rota Expressa. Você precisará executar o PowerShell como Administrador.

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
			


2. **Obtenha a lista de provedores, locais e larguras de banda com suporte.**

	Antes de criar um circuito da Rota Expressa você precisará de uma lista de provedores de conectividade, dos locais com suporte e de opções de largura de banda. O cmdlet *Get-AzureRmExpressRouteServiceProvider* do PowerShell retorna essas informações, que serão usadas em etapas posteriores.

		PS C:\> Get-AzureRmExpressRouteServiceProvider

	Verifique se o provedor de conectividade está listado. Anote o seguinte, pois essas informações serão necessárias para criar circuitos.
	
	- Nome
	- PeeringLocations
	- BandwidthsOffered

	Agora você está pronto para criar um circuito da Rota Expressa.

		
3. **Criar um circuito da Rota Expressa.**

	Primeiro crie um grupo de recursos, se ainda não tiver um, antes de criar o circuito da Rota Expressa. Você pode fazer isso executando o comando a seguir.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	O exemplo a seguir mostra como criar um circuito da Rota Expressa de 200 Mbps por meio da Equinix no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação.

	A seguir, um exemplo de solicitação de uma nova chave de serviço:

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Especifique a camada certa de SKU e a família de SKU.
 
	 - A camada de SKU determina se o complemento padrão da Rota Expressa ou da Rota Expressa Premium está habilitado. Você pode especificar o *padrão* para obter a SKU padrão ou *premium* para obter o complemento premium
	 - A família da SKU determina o tipo de cobrança. Você pode selecionar *metereddata* para um plano de dados limitado e *unlimiteddata"para um plano de dados ilimitado. **Observação:** não será possível alterar o tipo de cobrança depois de criar um circuito.

	A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help New-AzureRmExpressRouteCircuit -detailed 

4. **Lista com todos os circuitos de Rota Expressa.**

	Você pode executar o comando *Get-AzureDedicatedCircuit* para obter uma lista de todos os circuitos da Rota Expressa criados.

		#Getting service key
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
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


	Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureRmExpressRouteCircuit*. Fazer a chamada sem nenhum parâmetro listará todos os circuitos. Sua Chave de Serviço estará listada no campo *ServiceKey*.

		Get-AzureRmExpressRouteCircuit

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
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []



	Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help Get-AzureRmExpressRouteCircuit -detailed 

5. **Envie a Chave de Serviço ao seu provedor de conectividade para obter o provisionamento.**

	Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	O *ServiceProviderProvisioningState* fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço, e o Status fornece o estado no lado da Microsoft. Um circuito da Rota Expressa deverá estar no seguinte estado para você poder usá-lo.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Verifique periodicamente o status e o estado da chave do circuito.**

	Isso permite que você saiba quando seu provedor habilitou seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisionado*, conforme mostra o exemplo abaixo.

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

6. **Crie sua configuração de roteamento.**
	
	Consulte a página [Configuração de roteamento do circuito da Rota Expressa (criar e modificar emparelhamentos de circuito)](expressroute-howto-routing-arm.md) para obter instruções detalhadas.

7. **Vincule uma Rede Virtual a um circuito da Rota Expressa.**

	Em seguia, vincule uma Rede Virtual ao seu circuito da Rota Expressa. Veja [Vinculando circuitos da Rota Expressa a redes virtuais](expressroute-howto-linkvnet-arm.md) para obter instruções passo a passo. Se precisar criar uma rede virtual para a Rota Expressa, consulte [Criando uma rede virtual para a Rota Expressa](expressroute-howto-createvnet-classic.md) para obter instruções.

##  Obter o status de um circuito da Rota Expressa

Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureRmExpressRouteCircuit*. Fazer a chamada sem nenhum parâmetro listará todos os circuitos.

		Get-AzureRmExpressRouteCircuit

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

Você pode obter informações sobre um circuito da Rota Expressa específico passando o nome do grupo de recursos e o nome do circuito como um parâmetro para a chamada.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	The response will be something similar to the example below:

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

Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help get-azurededicatedcircuit -detailed 

## Modificar um circuito da Rota Expressa

Você pode modificar certas propriedades de um circuito da Rota Expressa sem afetar a conectividade.

Você pode fazer o seguinte:

- Habilitar/desabilitar o complemento premium da Rota Expressa para seu circuito da Rota Expressa sem qualquer período de inatividade.
- Aumente a largura de banda de seu circuito da Rota Expressa sem qualquer período de inatividade.

Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### Como habilitar o complemento premium da Rota Expressa

Você pode habilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte trecho do PowerShell:

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Name = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
O seu circuito agora tem os recursos do complemento premium da Rota Expressa habilitados. Observe que passaremos a cobrar pelo recurso do complemento Premium assim que o comando for executado com êxito.

### Como desabilitar o complemento premium da Rota Expressa

Você pode desabilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


O complemento Premium agora está desabilitado para o seu circuito.

Observe que esta operação poderá falhar se você estiver usando recursos que ultrapassam o que é permitido para o circuito padrão.

- Verifique se o número de redes virtuais vinculadas ao circuito é menor do que 10 antes de fazer o downgrade de Premium para padrão. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
- Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
- Sua tabela de roteamento deve ter menos do que 4000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior do que 4000 rotas, a sessão BGP será descartada e não poderá ser reabilitada até que o número de prefixos anunciados fique abaixo de 4000.


### Como atualizar a largura de banda do circuito da Rota Expressa

Verifique a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para ver as opções de largura de banda com suporte para o seu provedor. Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente. Depois de decidir o tamanho necessário, você poderá usar o seguinte comando para redimensionar o circuito.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

O circuito será sido dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações a fim de corresponder a essa alteração. Observe que passaremos a lhe cobrar pela opção de largura de banda atualizada a partir desse momento.

>[AZURE.IMPORTANT]Não é possível reduzir a largura de banda de um circuito da Rota Expressa sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito da Rota Expressa e um novo provisionamento de um novo circuito da Rota Expressa.

## Excluir e desprovisionar um circuito da Rota Expressa

Você pode excluir o circuito da Rota Expressa executando o comando a seguir:

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

Observe que você precisa desvincular todas as redes virtuais da Rota Expressa para que essa operação tenha sucesso. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de habilitado para *desabilitando*. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos envie uma notificação.

Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como *não provisionado*) antes da execução do cmdlet acima, desprovisionaremos o circuito e interromperemos a cobrança.

## Próximas etapas

- [Configurar o roteamento](expressroute-howto-routing-arm.md)

<!---HONumber=AcomDC_1203_2015-->