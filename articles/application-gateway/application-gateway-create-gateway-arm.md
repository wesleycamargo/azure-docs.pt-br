<properties 
   pageTitle="Criar, iniciar ou excluir um Application Gateway usando o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um Application Gateway do Azure usando o Gerenciador de Recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/07/2015"
   ms.author="joaoma"/>


# Criar, iniciar ou excluir um Application Gateway usando o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-create-gateway.md)
- [Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)

Nesta versão, você pode criar um Application Gateway usando o PowerShell ou chamadas à API REST. O suporte ao portal e à CLI será fornecido em uma versão futura. Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.

## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página de download](http://azure.microsoft.com/downloads/).
2. Você criará uma rede virtual e uma sub-rede para o Application Gateway. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Application Gateway deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configurará para usar o Application Gateway devem existir ou tem seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um Application Gateway?
 

- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. 
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no Application Gateway. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic*. A regra *basic* é a distribuição de carga round robin.


 
## Criar um novo Application Gateway

A diferença entre usar o Azure Classic e o Gerenciador de Recursos do Azure será a ordem em que você vai criar o application gateway e itens a serem configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um Application Gateway serão configurados individualmente e, em seguida, reunidos para criar o recurso do Application Gateway.


A seguir, as etapas necessárias para criar um Application Gateway:

1. Criar um grupo de recursos para o Gerenciador de Recursos
2. Crie uma rede virtual, uma sub-rede e um IP público para o Application Gateway
3. Criar um objeto de configuração do Application Gateway
4. Crie um recurso do Application Gateway


## Criar um grupo de recursos para o Gerenciador de Recursos

Alterne para o modo PowerShell para usar os cmdlets do ARM. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).

### Etapa 1

    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Etapa 2

Faça logon na sua conta do Azure.


    PS C:\> Add-AzureAccount

Você deverá autenticar com suas credenciais.


### Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.


### Etapa 4

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    PS C:\> New-AzureResourceGroup -Name appgw-rg -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Certifique-se de que todos os comandos para criar um Application Gateway usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-rg" e o local "Oeste dos EUA".

## Crie uma rede virtual e uma sub-rede para o Application Gateway

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos:

### Etapa 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Atribui o Intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual

### Etapa 2	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24
	
## Criar um endereço IP público para a configuração de front-end

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Cria um recurso de IP público "publicIP01" no grupo de recursos "appw-rg" para a região Oeste dos EUA.


## Criar um objeto de configuração do Application Gateway

### Etapa 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Cria uma configuração de IP do Application Gateway chamada "gatewayIP01". Quando o Application Gateway é iniciado, ele pega um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool IPs de back-end. Tenha em mente que cada instância terá um endereço IP.
 
### Etapa 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Esta etapa vai configurar o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses serão os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

### Etapa 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol HTTP -CookieBasedAffinity Disabled

Define as configurações de "poolsetting01" do Application Gateway para o tráfego de rede com carga balanceada no pool de back-end.

### Etapa 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura a porta do IP de front-end denominada "frontendport01" nesse caso para o ponto de extremidade do IP público.

### Etapa 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip

Cria a configuração do IP de front-end associando o endereço IP público à configuração de IP de front-end.

### Etapa 6

	$listener = New-AzureApplicationGatewayHttpListener -Name $listenerName  -Protocol http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Cria o ouvinte associando a porta de front-end à configuração de IP de front-end.

### Etapa 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cria a regra de roteamento do balanceador de carga, configurando seu comportamento.

### Etapa 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura o tamanho da instância do Application Gateway

>[AZURE.NOTE]O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Small, Medium e Large.

## Criar Application Gateway usando New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName $rgname -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Cria um Application Gateway com todos os itens de configuração das etapas acima. No exemplo, o Application Gateway se chama "appgwtest".




## Inicie o Application Gateway

Depois que o gateway tiver sido configurado, use o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.


**Observação:** o cmdlet `Start-AzureApplicationGateway` pode levar de 15 a 20 minutos para ser concluído.

No exemplo abaixo, o Application Gateway é chamado "appgwtest" e o grupo de recursos é "aplicativo rg":


### Etapa 1

Obtenha o objeto do Application Gateway e associe-o a uma variável de "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Etapa 2
	 
Use o `Start-AzureApplicationGateway` para iniciar o Application Gateway:

	PS C:\> Start-AzureApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verifique o status do Application Gateway

Use o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se *Start-AzureApplicationGateway* foi bem-sucedido na etapa anterior, o Estado deverá ser *Running*, e Vip e DnsName devem ter entradas válidas.

Este exemplo mostra um application gateway que está ativo, em execução e pronto para assumir o tráfego destinado a `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : appgwvnet 
	Subnets       : {Subnet01} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Excluir um Application Gateway

Para excluir um gateway de aplicativo, você precisará seguir este procedimento na ordem:

1. Use o cmdlet `Stop-AzureApplicationGateway` para interromper o gateway. 
2. Use o cmdlet `Remove-AzureApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet `Get-AzureApplicationGateway`.

Este exemplo mostra o cmdlet `Stop-AzureApplicationGateway` na primeira linha, seguido pela saída.

### Etapa 1

Obtenha o objeto do Application Gateway e associe-o a uma variável de "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Etapa 2
	 
Use o `Stop-AzureApplicationGateway` para parar o Application Gateway:

	PS C:\> Stop-AzureApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o Application Gateway estiver em um estado Stopped, use o cmdlet `Remove-AzureApplicationGateway` para remover o serviço.


	PS C:\> Remove-AzureApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]O comutador “-force” pode ser usado para suprimir a mensagem de confirmação da remoção
>

Para verificar se o serviço foi removido, você pode usar o cmdlet `Get-AzureApplicationGateway`. Essa etapa não é necessária.


	PS C:\>Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName app-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Próximas etapas

Se desejar configurar o descarregamento SSL, consulte [Configurar o Application Gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um application gateway para usar com o ILB, consulte [Criar um Application Gateway com um ILB (Balanceador de Carga Interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO7-->