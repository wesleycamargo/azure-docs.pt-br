<properties
   pageTitle="Etapas para configuração de um circuito da Rota Expressa usando PowerShell | Microsoft Azure"
   description="Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento de um circuito da Rota Expressa. Este artigo também mostra como verificar o status, atualizar ou excluir e desprovisionar seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
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
   ms.author="cherylmc"/>

# Criar e modificar um circuito da Rota Expressa usando o PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Este artigo fornece uma orientação pelas etapas de criação de um circuito da Rota Expressa usando cmdlets do PowerShell e o modelo de implantação clássico. As etapas a seguir também mostrarão a você como verificar o status, atualizar ou excluir e desprovisionar um circuito da Rota Expressa.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell. Baixe o módulo mais recente do PowerShell na seção PowerShell da [página Downloads do Azure](http://azure.microsoft.com/downloads). Siga as instruções na página [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter orientações detalhadas sobre como configurar o computador para usar os módulos do Azure PowerShell. 
- Examine a página [Pré-requisitos](expressroute-prerequisites.md) e a página [Fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## Criar e provisionar um circuito da Rota Expressa

1. **Importe o módulo do PowerShell para Rota Expressa.**

 	Para começar a usar os cmdlets da Rota Expressa, é necessário importar os módulos do Azure e da Rota Expressa para a sessão do PowerShell. Execute os seguintes comandos para importar os módulos do Azure e da Rota Expressa para a sessão do PowerShell.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obtenha a lista de provedores, locais e larguras de banda com suporte.**

	Antes de criar um circuito da Rota Expressa você precisará de uma lista de provedores de conectividade, dos locais com suporte e de opções de largura de banda. O cmdlet do PowerShell *Get-AzureDedicatedCircuitServiceProvider* retorna essas informações, que serão usadas em etapas posteriores.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Criar um circuito da Rota Expressa.**

	O exemplo a seguir mostra como criar um circuito da Rota Expressa de 200 Mbps por meio da Equinix no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação.

	A seguir, um exemplo de solicitação de uma nova chave de serviço:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	Ou, se você quiser criar um circuito da Rota Expressa com o complemento premium, use o exemplo a seguir. Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help new-azurededicatedcircuit -detailed 

4. **Lista com todos os circuitos de Rota Expressa.**

	Você pode executar o comando *Get-AzureDedicatedCircuit* para obter uma lista de todos os circuitos de Rota Expressa criados.

		#Getting service key
		Get-AzureDedicatedCircuit

	A resposta será algo semelhante ao exemplo a seguir:

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureDedicatedCircuit*. Fazer a chamada sem nenhum parâmetro listará todos os circuitos. Sua Chave de Serviço estará listada no campo *ServiceKey*.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help get-azurededicatedcircuit -detailed 

5. **Envie a Chave de Serviço ao seu provedor de conectividade para obter o provisionamento.**

	Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	O *ServiceProviderProvisioningState* fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço, e o Status fornece o estado no lado da Microsoft. Um circuito da Rota Expressa deverá estar no seguinte estado para você poder usá-lo.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Verifique periodicamente o status e o estado da chave do circuito.**

	Isso permite que você saiba quando seu provedor habilitou seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisionado*, conforme mostra o exemplo abaixo.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Crie sua configuração de roteamento.**
	
	Consulte a página [Configuração de roteamento do circuito da Rota Expressa (criar e modificar emparelhamentos de circuito)](expressroute-howto-routing-classic.md) para obter instruções detalhadas.

7. **Vincule uma Rede Virtual a um circuito da Rota Expressa.**

	Em seguia, vincule uma Rede Virtual ao seu circuito da Rota Expressa. Veja [Vinculando circuitos da Rota Expressa a redes virtuais](expressroute-howto-linkvnet-classic.md) para obter instruções passo a passo. Se você precisar criar uma rede virtual para a Rota Expressa, consulte [Criando uma rede virtual para a Rota Expressa](expressroute-howto-createvnet-classic.md) para obter instruções.

##  Obter o status de um circuito da Rota Expressa

Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureCircuit*. Fazer a chamada sem nenhum parâmetro listará todos os circuitos.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Você pode obter informações sobre um circuito da Rota Expressa específico passando a chave do serviço como um parâmetro para a chamada.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

		get-help get-azurededicatedcircuit -detailed 

##  Modificar um circuito da Rota Expressa

Você pode modificar certas propriedades de um circuito da Rota Expressa sem afetar a conectividade.

Você pode fazer o seguinte:

- Habilitar/desabilitar o complemento premium da Rota Expressa para seu circuito da Rota Expressa sem qualquer período de inatividade.
- Aumente a largura de banda de seu circuito da Rota Expressa sem qualquer período de inatividade.

Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### Como habilitar o complemento premium da Rota Expressa

Você pode habilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

O seu circuito agora tem os recursos do complemento premium da Rota Expressa habilitados. Observe que passaremos a cobrar pelo recurso do complemento Premium assim que o comando for executado com êxito.

### Como desabilitar o complemento premium da Rota Expressa

Você pode desabilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

O complemento Premium agora está desabilitado para o seu circuito.

>[AZURE.IMPORTANT]Esta operação poderá falhar se você estiver usando recursos que ultrapassam o que é permitido para o circuito padrão.
>
>- Verifique se o número de redes virtuais vinculadas ao circuito é menor do que 10 antes de fazer o downgrade de Premium para padrão. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
- Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
- Sua tabela de roteamento deve ter menos do que 4000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior do que 4000 rotas, a sessão BGP será descartada e não poderá ser reabilitada até que o número de prefixos anunciados fique abaixo de 4000.


### Como atualizar a largura de banda do circuito da Rota Expressa

Verifique a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para ver as opções de largura de banda com suporte para o seu provedor. Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente. Depois de decidir o tamanho necessário, você poderá usar o seguinte comando para redimensionar o circuito.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

O circuito será sido dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações a fim de corresponder a essa alteração. Observe que passaremos a lhe cobrar pela opção de largura de banda atualizada a partir desse momento.

>[AZURE.IMPORTANT]Não é possível reduzir a largura de banda de um circuito da Rota Expressa sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito da Rota Expressa e um novo provisionamento de um novo circuito da Rota Expressa.

##  Excluir e desprovisionar um circuito da Rota Expressa

Você pode excluir o circuito da Rota Expressa executando o comando a seguir:

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Observe que você precisa desvincular todas as redes virtuais da Rota Expressa para que essa operação tenha sucesso. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de habilitado para *desabilitando*. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos envie uma notificação.

Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como *não provisionado*) antes da execução do cmdlet acima, desprovisionaremos o circuito e interromperemos a cobrança.

## Próximas etapas

- [Configurar o roteamento](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_0121_2016-->