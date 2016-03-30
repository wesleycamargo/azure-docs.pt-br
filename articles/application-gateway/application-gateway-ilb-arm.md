<properties
   pageTitle="Criar e configurar um gateway de aplicativo com um ILB (balanceador de carga interno) usando o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Esta página oferece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure com um ILB (balanceador de carga interno) usando o Gerenciador de Recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>


# Criar um gateway de aplicativo com um ILB (balanceador de carga interno) usando o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Etapas do Azure clássico](application-gateway-ilb.md)
- [Etapas do PowerShell no Gerenciador de Recursos](application-gateway-ilb-arm.md)

O Azure Application Gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno não exposto à Internet, também conhecido como um ponto de extremidade ILB (balanceador de carga interno). Configurar o gateway como um ILB é útil para aplicativos de linha de negócios internos não expostos à Internet. Isso também é útil para serviços e camadas em um aplicativo multicamada que reside em um limite de segurança não exposto à Internet, mas que ainda exige distribuição de carga round robin, adesão da sessão ou terminação SSL.

Este artigo orientará você pelas etapas para configurar um gateway de aplicativo com um ILB.

## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você criará uma rede virtual e uma sub-rede para o Application Gateway. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Application Gateway deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configurará para usar o gateway de aplicativo deverão existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicativo?


- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à rede virtual, mas em uma sub-rede diferente para o gateway de aplicativo, ou devem ser um IP/VIP público.
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL).
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *básica*. A regra *básica* é a distribuição de carga round robin.



## Criar um novo gateway de aplicativo

A diferença entre usar o Azure Classic e o Gerenciador de Recursos do Azure é a ordem em que você vai criar o gateway de aplicativo e os itens que precisam ser configurados. Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.


A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
3. Criar um objeto de configuração do gateway do aplicativo
4. Criar um recurso do gateway de aplicativo


## Criar um grupo de recursos para o Gerenciador de Recursos

Alterne para o modo PowerShell para usar os cmdlets do Gerenciador de Recursos do Azure. Há mais informações disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### Etapa 1

		PS C:\> Login-AzureRmAccount

### Etapa 2

Verificar as assinaturas da conta.

		PS C:\> get-AzureRmSubscription

Você deverá se autenticar com suas credenciais.<BR>

### Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Etapa 4

Crie um novo grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-rg" e o local "Oeste dos EUA".

## Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos:

### Etapa 1

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Isso atribui o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

### Etapa 2

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Isso cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24

### Etapa 3

	$subnet=$vnet.subnets[0]

Isso atribui o objeto de sub-rede à variável $subnet para as próximas etapas.

## Criar um objeto de configuração do gateway do aplicativo

### Etapa 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Isso cria uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway é iniciado, ele obtém um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool IPs de back-end. Lembre-se de que cada instância usará um endereço IP.


### Etapa 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Isso configura o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses serão os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

### Etapa 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Isso define as configurações de "poolsetting01" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end.

### Etapa 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Isso configura a porta do IP de front-end denominada "frontendport01" para o ILB.

### Etapa 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Isso cria a configuração de IP de front-end chamada "fipconfig01" e a associa a um IP privado da sub-rede da rede virtual atual.

### Etapa 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Isso cria o ouvinte chamado "listener01" e associa a porta de front-end à configuração de IP de front-end.

### Etapa 7

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Isso cria a regra de roteamento do balanceador de carga chamada "rule01", configurando o comportamento do balanceador de carga.

### Etapa 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Isso configura o tamanho da instância do gateway de aplicativo.

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Standard\_Small, Standard\_Medium e Standard\_Large.

## Criar um gateway de aplicativo usando New-AzureApplicationGateway

Cria um gateway de aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado de "appgwtest".


	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Isso cria um gateway de aplicativo com todos os itens de configuração das etapas acima. No exemplo, o gateway de aplicativo se chama "appgwtest".


## Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo, você precisará seguir este procedimento na ordem:

1. Use o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway.
2. Use o cmdlet **Remove-AzureRmApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet **Get-AzureApplicationGateway**.


### Etapa 1

Obtenha o objeto do gateway de aplicativo e associe-o a uma variável "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Etapa 2

Use o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway de aplicativo. Este exemplo mostra o cmdlet **Stop-AzureRmApplicationGateway** na primeira linha, seguido pela saída.

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **Remove-AzureRmApplicationGateway** para remover o serviço.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] A opção **-force** pode ser usada para suprimir a mensagem de confirmação da remoção.


Para verificar se o serviço foi removido, você poderá usar o cmdlet **Get-AzureRmApplicationGateway**. Essa etapa não é necessária.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um application gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um Application Gateway para usar com um ILB, veja [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0323_2016-->