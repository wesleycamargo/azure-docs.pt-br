<properties
   pageTitle="Criar e modificar um circuito de Rota Expressa usando o modelo de implantação clássico e o PowerShell| Microsoft Azure"
   description="Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento de um circuito da Rota Expressa. Este artigo também mostra como verificar o status, atualizar ou excluir e desprovisionar seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
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

Este artigo fornece uma orientação pelas etapas de criação de um circuito da Rota Expressa usando cmdlets do PowerShell e o modelo de implantação clássico. Este artigo também mostrará a você como verificar o status, atualizar ou excluir e desprovisionar um circuito da Rota Expressa.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Antes de começar

- Você precisará da versão mais recente dos módulos do Azure PowerShell. Baixe o módulo mais recente do PowerShell na seção PowerShell da [página Downloads do Azure](https://azure.microsoft.com/downloads/). Siga as instruções na página [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter orientações detalhadas sobre como configurar o computador para usar os módulos do Azure PowerShell.

- Leia as páginas [Pré-requisitos](expressroute-prerequisites.md) e [Fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## Criar e provisionar um circuito da Rota Expressa

### 1\. Importar o módulo do PowerShell para Rota Expressa

 Para começar a usar os cmdlets da Rota Expressa, é necessário importar os módulos do Azure e da Rota Expressa para a sessão do PowerShell. Execute os seguintes comandos para importar os módulos do Azure e da Rota Expressa para a sessão do PowerShell.

	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### 2\. Obtenha a lista de provedores, locais e larguras de banda com suporte

Antes de criar um circuito de Rota Expressa você precisará de uma lista de provedores de conectividade, dos locais com suporte e de opções de largura de banda.

O cmdlet do PowerShell `Get-AzureDedicatedCircuitServiceProvider` retornará esta informação, que você usará em etapas posteriores.

	Get-AzureDedicatedCircuitServiceProvider

Verifique se o provedor de conectividade está listado. Anote as informações a seguir, você precisará delas posteriormente ao criar um circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora você está pronto para criar um circuito da Rota Expressa.

### 3\. Criar um circuito da Rota Expressa

O exemplo a seguir mostra como criar um circuito da Rota Expressa de 200 Mbps por meio da Equinix no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação.

>[AZURE.IMPORTANT] O circuito da Rota Expressa será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação assim que o provedor de conectividade estiver pronto para provisionar o circuito.


A seguir, um exemplo de solicitação de uma nova chave de serviço:

	$Bandwidth = 200
	$CircuitName = "MyTestCircuit"
	$ServiceProvider = "Equinix"
	$Location = "Silicon Valley"

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

Ou, se você quiser criar um circuito da Rota Expressa com o complemento premium, use o exemplo a seguir. Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

	get-help new-azurededicatedcircuit -detailed 

### 4\. Listar todos os circuitos da Rota Expressa

Você pode executar o comando *Get-AzureDedicatedCircuit* para obter uma lista de todos os circuitos de Rota Expressa criados.

		
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

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureDedicatedCircuit`. Fazer a chamada sem nenhum parâmetro listará todos os circuitos. Sua Chave de Serviço estará listada no campo *ServiceKey*.

	Get-AzureDedicatedCircuit

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

### 5\. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento


O *ServiceProviderProvisioningState* fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço, e o Status fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, consulte o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:
	
	ServiceProviderProvisioningState : NotProvisioned	
	Status                           : Enabled


O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Um circuito da Rota Expressa deverá estar no seguinte estado para você poder usá-lo.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled


### 6\. Verifique periodicamente o status e o estado da chave do circuito

Isso permite que você saiba quando seu provedor habilitou seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisionado*, conforme mostra o exemplo abaixo.

	Get-AzureDedicatedCircuit

	Bandwidth                        : 200
	CircuitName                      : MyTestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled

### 7\. Criar sua configuração de roteamento
	
Consulte a página [Configuração de roteamento do circuito da Rota Expressa (criar e modificar emparelhamentos de circuito)](expressroute-howto-routing-classic.md) para obter instruções detalhadas.

>[AZURE.IMPORTANT] Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de Camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

### 8\. Vincular uma rede virtual a um circuito da Rota Expressa

Em seguia, vincule uma Rede Virtual ao seu circuito da Rota Expressa. Veja [Vinculando circuitos da Rota Expressa a redes virtuais](expressroute-howto-linkvnet-classic.md) para obter instruções passo a passo. Se precisa criar uma rede virtual usando o modelo de implantação clássica da Rota Expressa, confira [Configurar uma rede virtual para Rota Expressa](expressroute-howto-vnet-portal-classic.md) para obter instruções.



##  Obtendo o status de um circuito da Rota Expressa

Você pode recuperar essas informações a qualquer momento usando o cmdlet *Get-AzureCircuit*. Fazer a chamada sem nenhum parâmetro listará todos os circuitos.

	Get-AzureDedicatedCircuit

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

	Get-AzureDedicatedCircuit -ServiceKey "*********************************"

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

##  Modificando um circuito da Rota Expressa

Você pode modificar certas propriedades de um circuito da Rota Expressa sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

- Como habilitar ou desabilitar o complemento premium da Rota Expressa para seu circuito da Rota Expressa.
- Aumente a largura de banda de seu circuito da Rota Expressa. Observe que não há suporte para o downgrade da largura de banda de um circuito. 
- Alterar o plano de medição de dados limitados para ilimitados. Observe que não há suporte para a alteração do plano de medição de dados ilimitados para limitados. 
-  Você pode habilitar e desabilitar "Permitir Operações Clássicas" 

Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### Para habilitar o complemento premium da Rota Expressa

Você pode habilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
	Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Premium
	Status                           : Enabled

O seu circuito agora tem os recursos do complemento premium da Rota Expressa habilitados. Observe que passaremos a cobrar pelo recurso do complemento Premium assim que o comando for executado com êxito.

### Para desabilitar o complemento premium da Rota Expressa

>[AZURE.IMPORTANT] Esta operação poderá falhar se você estiver usando recursos que ultrapassam o que é permitido para o circuito padrão.

Observe o seguinte:

- Verifique se o número de redes virtuais vinculadas ao circuito é menor do que 10 antes de fazer o downgrade de Premium para padrão. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.

- Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.

- Sua tabela de roteamento deve ter menos do que 4000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior do que 4000 rotas, a sessão BGP será descartada e não poderá ser reabilitada até que o número de prefixos anunciados fique abaixo de 4000.

Você pode desabilitar o complemento premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
	Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled



### Para atualizar a largura de banda do circuito da Rota Expressa

Verifique a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para ver as opções de largura de banda com suporte para o seu provedor. Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente.

>[AZURE.IMPORTANT] Não é possível reduzir a largura de banda de um circuito da Rota Expressa sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito da Rota Expressa e um novo provisionamento de um novo circuito da Rota Expressa.

Depois de decidir o tamanho necessário, você poderá usar o seguinte comando para redimensionar o circuito.

	Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled

O circuito será sido dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações a fim de corresponder a essa alteração. Observe que passaremos a lhe cobrar pela opção de largura de banda atualizada a partir desse momento.



## Excluindo e desprovisionando um circuito da Rota Expressa

Observe o seguinte:

- Você deve desvincular todas as redes virtuais da Rota Expressa para que essa operação seja bem-sucedida. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

- Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de habilitado para *desabilitando*. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos envie uma notificação.

- Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como *não provisionado*) antes da execução do cmdlet acima, desprovisionaremos o circuito e interromperemos a cobrança.

Você pode excluir o circuito da Rota Expressa executando o comando a seguir:

	Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	



## Próximas etapas

Depois de criar seu circuito, faça o seguinte:

- [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-classic.md)
- [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0427_2016-->