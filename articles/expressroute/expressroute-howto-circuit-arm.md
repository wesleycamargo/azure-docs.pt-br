<properties
   pageTitle="Criar e modificar um circuito da Rota Expressa usando o Gerenciador de Recursos e o PowerShell | Microsoft Azure"
   description="Este artigo descreve como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito da Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="ganesr"/>


# Criar e modificar um circuito da Rota Expressa

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)


Este artigo descreve como criar um circuito da Rota Expressa do azure usando os cmdlets do Windows PowerShell e o modelo de implantação do Azure Resource Manager. Este artigo também mostrará a você como verificar o status do circuito, como atualizá-lo ou como excluí-lo e desprovisioná-lo.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Antes de começar


- Obtenha a versão mais recente dos módulos do Azure PowerShell (pelo menos a versão 1.0). Para obter as diretrizes passo a passo sobre como configurar seu computador para usar os módulos do PowerShell, siga as instruções em [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## Criar e provisionar um circuito da Rota Expressa

### 1\. Entre na sua conta do Azure e selecione sua assinatura

Para iniciar sua configuração, entrar na sua conta do Azure. Para obter mais informações sobre o PowerShell, consulte [Usando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md). Use o exemplo a seguir para ajudar a conectar:

	Login-AzureRmAccount

Verifique as assinaturas da conta:

	Get-AzureRmSubscription

Selecione a assinatura para a qual você deseja criar um circuito da Rota Expressa:

	Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### 2\. Obtenha a lista de provedores, de locais e de larguras de banda com suporte

Antes de criar um circuito de Rota Expressa você precisará de uma lista de provedores de conectividade com suporte, dos locais e de opções de largura de banda.

O cmdlet do PowerShell `Get-AzureRmExpressRouteServiceProvider` retornará estas informações, que você usará em etapas posteriores:

	Get-AzureRmExpressRouteServiceProvider

Verifique se o provedor de conectividade está listado. Anote as informações a seguir, pois você precisará delas mais tarde quando criar um circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora você está pronto para criar um circuito da Rota Expressa.

### 3\. Criar um circuito da Rota Expressa

Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar o circuito da Rota Expressa. Faça isso ao executar o seguinte comando:


	New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


O exemplo a seguir mostra como criar um circuito da Rota Expressa de 200 Mbps por meio da Equinix, no Vale do Silício. Se estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a solicitação. A seguir, um exemplo de solicitação de uma nova chave de serviço:

	New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Especifique a camada da SKU e a família de SKUs corretas:

- A camada da SKU determina se um complemento padrão ou premium da Rota Expressa está habilitado. Você pode especificar *Standard* para obter a SKU padrão ou *Premium* para o complemento premium.

- A família da SKU determina o tipo de cobrança. Você pode especificar *Metereddata* para um plano de dados limitado e *Unlimiteddata* para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança de *Metereddata* para *Unlimiteddata*, mas não pode alterar o tipo de *Unlimiteddata* para *Metereddata*.


>[AZURE.IMPORTANT] O circuito da Rota Expressa será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.

A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:


	get-help New-AzureRmExpressRouteCircuit -detailed


### 4\. Listar todos os circuitos da Rota Expressa

Para obter uma lista de todos os circuitos da Rota Expressa criados, execute o comando `Get-AzureRmExpressRouteCircuit`:


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

A resposta será semelhante ao seguinte exemplo:


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
	CircuitProvisioningState          : Enabled
	ServiceProviderProvisioningState  : NotProvisioned
	ServiceProviderNotes              :
	ServiceProviderProperties         : {
	                                      "ServiceProviderName": "Equinix",
	                                      "PeeringLocation": "Silicon Valley",
	                                      "BandwidthInMbps": 200
	                                    }
	ServiceKey                        : **************************************
	Peerings                          : []

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureRmExpressRouteCircuit`. Fazer a chamada sem parâmetros listará todos os circuitos. Sua chave de serviço estará listada no campo *ServiceKey*:


	Get-AzureRmExpressRouteCircuit


A resposta será semelhante ao seguinte exemplo:


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

### 5\. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento

*ServiceProviderProvisioningState* fornece informações sobre o estado atual de provisionamento no lado do provedor de serviço. Status fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:


	ServiceProviderProvisioningState : NotProvisioned
	CircuitProvisioningState         : Enabled



O circuito assumirá o estado a seguir quando o provedor de conectividade estiver no processo de habilitá-lo para você:

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Para que você consiga usar um circuito da Rota Expressa, ele deverá estar no seguinte estado:

	ServiceProviderProvisioningState : Provisioned
	CircuitProvisioningState         : Enabled

### 6\. Verifique periodicamente o status e o estado da chave do circuito

A verificação do status e o estado da chave do circuito informará quando o provedor tiver habilitado seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisioned*, como mostrado neste exemplo:


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta será semelhante ao seguinte exemplo:


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

### 7\. Criar sua configuração de roteamento

Para obter instruções passo a passo, confira o artigo [configuração do roteamento de circuito da Rota Expressa](expressroute-howto-routing-arm.md) para criar e modificar os emparelhamentos de circuito.


>[AZURE.IMPORTANT] Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

### 8\. Vincular uma rede virtual a um circuito de Rota Expressa

Em seguida, vincule uma rede virtual a seu circuito da Rota Expressa. Use o artigo [Vincular redes virtuais a circuitos da Rota Expressa](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de Recursos.

## Obtendo o status de um circuito da Rota Expressa

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureRmExpressRouteCircuit`. Fazer a chamada sem parâmetros listará todos os circuitos.

	Get-AzureRmExpressRouteCircuit


A resposta será semelhante ao seguinte exemplo:


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


Você pode obter informações sobre um circuito da Rota Expressa específico passando o nome do grupo de recursos e o nome do circuito como um parâmetro para a chamada:


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta será semelhante ao seguinte exemplo:


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


## <a name="modify"></a>Modificar um circuito da Rota Expressa

Você pode modificar certas propriedades de um circuito da Rota Expressa sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

- Como habilitar ou desabilitar o complemento premium da Rota Expressa para seu circuito da Rota Expressa.
- Aumente a largura de banda de seu circuito da Rota Expressa. Observe que não há suporte para o downgrade da largura de banda de um circuito.
- Altere o plano de medição de Dados Limitados para Dados Ilimitados. Observe que a alteração do plano de medição de Dados Ilimitados para Dados Limitados não tem suporte.
-  Você pode habilitar e desabilitar *Permitir Operações Clássicas*.

Para saber mais sobre limites e limitações, confira as [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md).

### Para habilitar o complemento premium da Rota Expressa

Você pode habilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte trecho do PowerShell:

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Premium"
	$ckt.sku.Name = "Premium_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Agora, o circuito terá os recursos de complemento premium da Rota Expressa habilitados. Observe que começaremos a cobrar pelo recurso do complemento Premium assim que o comando for executado com êxito.

### Para desabilitar o complemento premium da Rota Expressa

>[AZURE.IMPORTANT] Esta operação poderá falhar se você estiver usando recursos que ultrapassem o que é permitido para o circuito padrão.

Observe o seguinte:

- Antes de fazer o downgrade de premium para standard, verifique se o número de redes virtuais vinculadas ao circuito é menor que 10. Se você não fizer isso, sua solicitação de atualização falhará e cobraremos com tarifas premium.

- Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Se você não fizer isso, sua solicitação de atualização falhará e cobraremos com tarifas premium.

- Sua tabela de roteamento deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior que 4.000 rotas, a sessão BGP será descartada e não poderá ser reabilitada até que o número de prefixos anunciados fique abaixo de 4.000.

Você pode desabilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Standard"
	$ckt.sku.Name = "Standard_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Para atualizar a largura de banda do circuito da Rota Expressa

Para obter opções de largura de banda com suporte para seu provedor, confira as [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md). Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente.

>[AZURE.IMPORTANT] Não é possível reduzir a largura de banda de um circuito da Rota Expressa sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito da Rota Expressa e um reprovisionamento de um novo circuito da Rota Expressa.

Depois de decidir sobre qual tamanho você precisa, use o seguinte comando para redimensionar o circuito:


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


O circuito será dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações para corresponder a essa alteração. Depois que você fizer essa notificação, começaremos a cobrar pela opção de largura de banda atualizada.


### Para mover a SKU de limitado para ilimitado

Você pode alterar a SKU de um circuito de Rota Expressa usando o seguinte trecho de código do PowerShell:

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Family = "UnlimitedData"
	$ckt.sku.Name = "Premium_UnlimitedData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Para controlar o acesso aos ambientes clássico e do Resource Manager  

Confira as instruções em [Mover os circuitos de Rota Expressa do modelo de implantação Clássico para o Resource Manager](expressroute-howto-move-arm.md).


## Excluindo e desprovisionando um circuito da Rota Expressa

Observe o seguinte:

- Você deve desvincular todas as redes virtuais do circuito da Rota Expressa. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

- Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de *Desabilitando* para um estado habilitado. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.

- Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como *Não provisionado*) antes da execução do cmdlet anterior, desprovisionaremos o circuito e interromperemos a cobrança.

Você pode excluir o circuito da Rota Expressa executando o comando a seguir:

	Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## Próximas etapas

Depois de criar seu circuito, faça o seguinte:

- [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-arm.md)
- [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0518_2016-->