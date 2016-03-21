<properties
   pageTitle="Criar e modificar um circuito da Rota Expressa usando o Gerenciador de Recursos e o PowerShell | Microsoft Azure"
   description="Este artigo descreve como criar e provisionar um circuito da Rota Expressa. Ele também mostra como verificar o circuito, como atualizá-lo ou como excluí-lo e desprovisioná-lo."
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
   ms.date="03/03/2016"
   ms.author="cherylmc"/>

# Criar e modificar um circuito da Rota Expressa usando o Gerenciador de Recursos e o PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Este artigo descreve como criar um circuito da Rota Expressa do azure usando os cmdlets do Windows PowerShell e o modelo de implantação do Azure Resource Manager. As etapas a seguir também mostrarão a você como verificar o status do circuito, como atualizá-lo ou como excluí-lo e desprovisioná-lo.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Pré-requisitos de configuração

Para criar um circuito da Rota Expressa, você precisará:

- Obter a versão mais recente dos módulos do Azure PowerShell, versão 1.0 ou posterior. Para obter as diretrizes passo a passo sobre como configurar seu computador para usar os módulos do PowerShell, siga as instruções da página [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
- Examine a página [Pré-requisitos](expressroute-prerequisites.md) e a página [Fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

## Criar e provisionar um circuito da Rota Expressa

**Etapa 1. Importe o módulo do PowerShell para a Rota Expressa.**

Para começar a usar os cmdlets da Rota Expressa, você deverá instalar o instalador mais recente do PowerShell da [galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Gerenciador de Recursos para a sessão do PowerShell. Você precisará executar o PowerShell como administrador.

```
Install-Module AzureRM

Install-AzureRM
```

Importe todos os módulos do AzureRM que estejam no intervalo de versão semântica conhecida:

```
Import-AzureRM
```

Você também pode importar um módulo selecionado dentro do intervalo de versão semântica conhecida:

```
Import-Module AzureRM.Network
```

Entre na sua conta:

```
Login-AzureRmAccount
```

Selecione a assinatura na qual você deseja criar um circuito da Rota Expressa:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Etapa 2. Obtenha a lista de provedores, de locais e de larguras de banda com suporte.**

Antes de criar um circuito da Rota Expressa você precisará de uma lista de provedores de conectividade, dos locais com suporte e de opções de largura de banda. O cmdlet *Get-AzureRmExpressRouteServiceProvider* do PowerShell retorna essas informações, que serão usadas em etapas posteriores.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Verifique se o provedor de conectividade está listado. Anote as informações a seguir, você precisará delas posteriormente ao criar um circuito:

- Nome
- PeeringLocations
- BandwidthsOffered

Agora você está pronto para criar um circuito da Rota Expressa.

**Etapa 3. Crie um circuito da Rota Expressa.**

Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar o circuito da Rota Expressa. Faça isso ao executar o seguinte comando:

```
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo a seguir mostra como criar um circuito da Rota Expressa de 200 Mbps por meio da Equinix, no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação. A seguir, um exemplo de solicitação de uma nova chave de serviço:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Especifique a camada da SKU e a família de SKUs corretas:

- A camada da SKU determina se um complemento padrão ou premium da Rota Expressa está habilitado. Você pode especificar o *padrão* para obter a SKU padrão ou o *premium* para o complemento premium
- A família da SKU determina o tipo de cobrança. Você pode selecionar *metereddata* para um plano de dados limitado e *unlimiteddata*para um plano de dados ilimitado. **Observação:** não será possível alterar o tipo de cobrança depois de criar um circuito.

A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros ao executar o seguinte:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Etapa 4. Lista todos os circuitos da Rota Expressa.**

Para obter uma lista de todos os circuitos da Rota Expressa criados, execute o comando *Get-AzureRmExpressRouteCircuit*:

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta será semelhante ao seguinte exemplo:

```
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
```

Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureRmExpressRouteCircuit*. Fazer a chamada sem parâmetros listará todos os circuitos. Sua chave de serviço estará listada no campo *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

A resposta será semelhante ao seguinte exemplo:

```
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
```

Você pode obter descrições detalhadas de todos os parâmetros ao executar o seguinte:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Etapa 5. Envie a chave de serviço ao seu provedor de conectividade para obter o provisionamento.**

Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

O *ServiceProviderProvisioningState* fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço e o Status fornece o estado no lado da Microsoft. Para que você consiga usar um circuito da Rota Expressa, ele deverá estar no seguinte estado:

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

O circuito assumirá o estado a seguir quando o provedor de conectividade estiver no processo de habilitá-lo para você:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Etapa 6: Verifique periodicamente o status e o estado da chave do circuito.**

A verificação do status e o estado da chave do circuito informará você quando seu provedor tiver habilitado seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisionado*, como mostrado neste exemplo:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta será semelhante ao seguinte exemplo:

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Observe que você precisa desvincular todas as redes virtuais do circuito da Rota Expressa para que essa operação tenha sucesso. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de habilitado para *desabilitando*. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. A Microsoft continuará a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.

Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como *não provisionado*) antes da execução do cmdlet acima, a Microsoft desprovisionará o circuito e interromperemos a cobrança.

## Próximas etapas

Depois de criar seu circuito, faça o seguinte:

- [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-arm.md)
- [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->