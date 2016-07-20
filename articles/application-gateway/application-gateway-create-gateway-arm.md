<properties
   pageTitle="Criar, iniciar ou excluir um gateway de aplicativo usando o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure usando o Gerenciador de Recursos do Azure"
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
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Criar, iniciar ou excluir um gateway de aplicativo usando o Gerenciador de Recursos do Azure

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL.


> [AZURE.SELECTOR]
- [Etapas do PowerShell Clássico do Azure](application-gateway-create-gateway.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)


<BR>


Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.


>[AZURE.IMPORTANT] Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Gerenciador de Recursos e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo. Este documento abordará a criação de um gateway de aplicativo usando o Gerenciador de Recursos do Azure. Para usar a versão clássica, vá para [Criar uma implantação clássica do gateway de aplicativo usando o PowerShell](application-gateway-create-gateway.md).



## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Se você tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou crie uma nova sub-rede na rede virtual existente unicamente para uso pelo gateway de aplicativo. Não é possível implantar o gateway de aplicativo para uma rede virtual diferente dos recursos que você pretende implantar por trás do gateway de aplicativo.
3. Os servidores que você configurará para usar o gateway de aplicativo deverão existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicativo?


- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL).
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico.



## Criar um novo gateway de aplicativo

A diferença entre usar o Azure Classic e o Gerenciador de Recursos do Azure é a ordem em que você vai criar o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.


A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo.
3. Criar um objeto de configuração do gateway do aplicativo.
4. Criar um recurso de gateway de aplicativo.


## Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### Etapa 1
Faça logon em Login-AzureRmAccount do Azure

Você deverá se autenticar com suas credenciais.<BR>
### Etapa 2
Verificar as assinaturas da conta.

		Get-AzureRmSubscription

### Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar.<BR>

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Etapa 4
Crie um novo grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

>[AZURE.NOTE] Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.



## Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos.

### Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Etapa 2

Crie uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Etapa 3

Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

	$subnet=$vnet.Subnets[0]

## Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Criar um objeto de configuração do gateway do aplicativo

Você precisa configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway for iniciado, ele escolherá um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Lembre-se de que cada instância usará um endereço IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Etapa 2

Configure o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses serão os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Etapa 3

Defina as configurações de "poolsetting01" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Etapa 4

Configure a porta do IP de front-end denominada "frontendport01" para o ponto de extremidade do IP público.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Etapa 5

Crie a configuração de IP de front-end chamada "fipconfig01" e associe o endereço IP público à configuração de IP de front-end.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Etapa 6

Crie o nome de ouvinte "listener01" e associe a porta de front-end à configuração de IP de front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Etapa 7

Crie a regra de roteamento do balanceador de carga chamada "rule01", que configura o comportamento do balanceador de carga.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Etapa 8

Configure o tamanho da instância do gateway de aplicativo.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Standard\_Small, Standard\_Medium e Standard\_Large.

## Criar um gateway de aplicativo usando New-AzureRmApplicationGateway

Crie um gateway de aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado de "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### Etapa 9
Recupere os detalhes de DNS e VIP do gateway de aplicativo do recurso de IP público anexado ao gateway de aplicativo.

	Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

	Name                     : publicIP01
	ResourceGroupName        : appgwtest 
	Location                 : westus
	Id                       : /subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"12302060-78d6-4a33-942b-a494d6323767"
	ResourceGuid             : ee9gd76a-3gf6-4236-aca4-gc1f4gf14171
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : 137.116.26.16
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fipconfig01"
	                           }
	DnsSettings              : {
	                             "Fqdn": "ee7aca47-4344-4810-a999-2c631b73e3cd.cloudapp.net"
	                           } 



## Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo, siga estas etapas:

1. Use o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway.
2. Use o cmdlet **Remove-AzureRmApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet **Get-AzureRmApplicationGateway**.

### Etapa 1

Obtenha o objeto do gateway de aplicativo e associe-o a uma variável "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Etapa 2

Use o cmdlet **Stop-AzureRmApplicationGateway** para parar o gateway de aplicativo.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **Remove-AzureRmApplicationGateway** para remover o serviço.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] A opção **-force** pode ser usada para suprimir a mensagem de confirmação da remoção.


Para verificar se o serviço foi removido, você pode usar o cmdlet **Get-AzureRmApplicationGateway**. Essa etapa não é necessária.


	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Próximas etapas

Para configurar o descarregamento SSL, confira [Configurar um gateway de aplicativo para descarregamento SSL](application-gateway-ssl.md).

Para configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0713_2016-->