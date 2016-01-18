<properties 
   pageTitle="Criar investigação personalizada para o Application Gateway usando o PowerShell no Gerenciador de Recursos | Microsoft Azure"
   description="Saiba como criar uma investigação personalizada para o Application Gateway usando o PowerShell no Gerenciador de Recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Criar investigação personalizada para o Application Gateway usando o PowerShell para o Gerenciador de Recursos do Azure

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Etapa 1 

Usar Login-AzureRmAccount para autenticar

	Login-AzureRmAccount

### Etapa 2

Verificar as assinaturas da conta

		get-AzureRmSubscription 

Você deverá se autenticar com suas credenciais.<BR>

### Etapa 3 

Escolha quais das suas assinaturas do Azure deseja usar.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Etapa 4

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Certifique-se de que todos os comandos para criar um Application Gateway usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-rg" e o local "Oeste dos EUA".

## Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

As seguintes etapas criarão uma rede virtual e uma sub-rede para o Application Gateway

### Etapa 1	
	

Atribui o Intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Etapa 2	

Cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Etapa 3

Atribuir uma variável de sub-rede para as próximas etapas criando um gateway de aplicativo
		
	$subnet=$vnet.Subnets[0]

## Criar um endereço IP público para a configuração de front-end


Cria um recurso de IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
 

## Criar um objeto de configuração do Application Gateway com investigação personalizada 

Você precisa configurar todos os itens de configuração antes de criar o Application Gateway do aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo


### Etapa 1

Cria uma configuração de IP do Application Gateway chamada "gatewayIP01". Quando o Application Gateway é iniciado, ele pega um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool IPs de back-end. Tenha em mente que cada instância terá um endereço IP.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Etapa 2


Esta etapa vai configurar o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses serão os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Etapa 3


A investigação personalizada é configurada nesta etapa.

Os parâmetros usados são:

- **-Interval** - configura as verificações de intervalo de investigação em segundos 
- **-Timeout** - define o tempo limite da investigação para uma verificação de resposta HTTP
- **-Hostname e -path** - caminho de URL completo que é invocado pelo Application Gateway para determinar a integridade da instância. Por exemplo: você tem um site http://contoso.com/ e a investigação personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para verificações de investigação com resposta HTTP bem-sucedida. 
- **-UnhealthyThreshold** - o número de respostas HTTP com falha; ele é necessário para sinalizar a instância de back-end como *não íntegra*



	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Etapa 4

Define as configurações "poolsetting01" do Application Gateway para o tráfego no pool de back-end. Essa etapa também tem uma configuração de tempo limite para a resposta do pool de back-end para uma solicitação do Application Gateway. Quando uma resposta de back-end atinge um tempo limite, o Application Gateway cancela a solicitação. Isso é diferente do tempo limite da investigação, que é somente para a resposta do back-end a verificações de investigação.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Etapa 5

Configura a porta do IP de front-end denominada "frontendport01" nesse caso para o ponto de extremidade do IP público.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Etapa 6

Cria a configuração de IP de front-end chamada "fipconfig01" e associa o endereço IP público à configuração de IP de front-end.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Etapa 7

Cria o ouvinte de nome "listener01" e associa a porta de front-end à configuração de IP de front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Etapa 8 

Cria a regra de roteamento do balanceador de carga chamada "rule01" configurando o comportamento do balanceador de carga.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Etapa 9

Configura o tamanho da instância do Application Gateway

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Médio. Você pode escolher entre Standard\_Small, Standard\_Medium e Standard\_Large.

## Criar um Application Gateway usando New-AzureRmApplicationGateway

Cria um Application Gateway com todos os itens de configuração das etapas acima. No exemplo, o Application Gateway se chama "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Adicionar uma investigação a um Application Gateway existente

Você tem quatro etapas para adicionar uma investigação personalizada a um Application Gateway existente.

### Etapa 1 

Carregar o recurso do Application Gateway em uma variável do PowerShell usando `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Etapa 2

Adicionar uma investigação à configuração de gateway existente.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

   
No exemplo, a investigação personalizada é configurada para verificar o caminho de URL contoso.com/path/custompath.htm a cada 30 segundos. Um tempo limite de 120 segundos é configurado com o número máximo de 8 solicitações de investigação com falha.

### Etapa 3

Adicionar a investigação à configuração do pool de back-end e tempo limite usando `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Etapa 4

Salvar a configuração no Application Gateway usando `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Remover uma investigação de um Application Gateway existente

Estas são as etapas para remover uma investigação personalizada de um Application Gateway existente.

### Etapa 1 

Carregar o recurso do Application Gateway em uma variável do PowerShell usando `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Etapa 2 

Remover a configuração de investigação do Application Gateway usando `Remove-AzureRmApplicationGatewayProbeConfig`

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Etapa 3 

Atualizar a configuração do pool de back-end para remover a investigação e o tempo limite usando `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Etapa 4
	
Salvar a configuração no Application Gateway usando `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0107_2016-->