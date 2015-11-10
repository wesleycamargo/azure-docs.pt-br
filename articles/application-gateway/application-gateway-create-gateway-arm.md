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
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Criar, iniciar ou excluir um Application Gateway usando o Gerenciador de Recursos do Azure

O Application Gateway é a camada 7 do balanceador de carga. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie, descarregamento SSL.


> [AZURE.SELECTOR]
- [Azure Classic Powershell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template steps](application-gateway-create-gateway-arm-template.md)


<BR>


Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.


>[AZURE.IMPORTANT]Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Gerenciador de Recursos e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação de diferentes ferramentas clicando nas guias na parte superior deste artigo. Este documento abordará a criação de um Application Gateway usando o Gerenciador de Recursos do Azure. Para usar a versão clássica, vá para [criar uma implantação clássica de Application Gateway usando o PowerShell](application-gateway-create-gateway.md).



## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [Página de download](http://azure.microsoft.com/downloads/).
2. Você criará uma rede virtual e uma sub-rede para o Application Gateway. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Application Gateway deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configurará para usar o Application Gateway devem existir ou tem seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um Application Gateway?
 

- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. 
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa é a porta pública aberta no Application Gateway. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado quando atingir um ouvinte específico. Atualmente, há suporte apenas para a regra *básica*. A regra *básica* é a distribuição de carga round robin.


 
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

    Switch-AzureMode -Name AzureResourceManager

### Etapa 2

Faça logon na sua conta do Azure.


    Add-AzureAccount

Você deverá autenticar com suas credenciais.


### Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

    Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.


### Etapa 4

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    New-AzureResourceGroup -Name appgw-rg -location "West US"

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

### Etapa 3
	
	$subnet=$vnet.Subnets[0]

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

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Define as configurações de "poolsetting01" do Application Gateway para o tráfego de rede com carga balanceada no pool de back-end.

### Etapa 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura a porta do IP de front-end denominada "frontendport01" nesse caso para o ponto de extremidade do IP público.

### Etapa 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Cria a configuração de IP de front-end chamada "fipconfig01" e associa o endereço IP público à configuração de IP de front-end.

### Etapa 6

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Cria o ouvinte de nome "listener01" e associa a porta de front-end à configuração de IP de front-end.

### Etapa 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cria a regra de roteamento do balanceador de carga chamada "rule01" configurando o comportamento do balanceador de carga.

### Etapa 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura o tamanho da instância do Application Gateway

>[AZURE.NOTE]O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Médio. Você pode escolher entre Standard\_Small, Standard\_Medium e Standard\_Large.

## Criar Application Gateway usando New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Cria um Application Gateway com todos os itens de configuração das etapas acima. No exemplo, o Application Gateway se chama "appgwtest".


## Inicie o Application Gateway

Depois que o gateway tiver sido configurado, use o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.


**Observação:** a conclusão do cmdlet `Start-AzureApplicationGateway` pode demorar de 15 a 20 minutos.

No exemplo abaixo, o Application Gateway é chamado "appgwtest" e o grupo de recursos é "aplicativo rg":


### Etapa 1

Obtenha o objeto do Application Gateway e associe-o a uma variável de "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Etapa 2
	 
Use o `Start-AzureApplicationGateway` para iniciar o Application Gateway:

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## Verifique o status do Application Gateway

Use o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se *Start-AzureApplicationGateway* tiver sido bem-sucedido na etapa anterior, o Estado deverá ser *Em Execução*, e Vip e DnsName devem ter entradas válidas.

Este exemplo mostra um application gateway que está ativo, em execução e pronto para assumir o tráfego destinado a `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
		Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## Excluir um Application Gateway

Para excluir um gateway de aplicativo, você precisará seguir este procedimento na ordem:

1. Use o cmdlet `Stop-AzureApplicationGateway` para parar o gateway. 
2. Use o cmdlet `Remove-AzureApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet `Get-AzureApplicationGateway`.


### Etapa 1

Obtenha o objeto do Application Gateway e associe-o a uma variável de "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Etapa 2
	 
Use o `Stop-AzureApplicationGateway` para parar o Application Gateway:

	Stop-AzureApplicationGateway -ApplicationGateway $getgw  


Depois que o Application Gateway estiver em um estado Parado, use o cmdlet `Remove-AzureApplicationGateway` para remover o serviço.


	Remove-AzureApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]O comutador “-force” pode ser usado para suprimir a mensagem de confirmação da remoção
>

Para verificar se o serviço foi removido, você pode usar o cmdlet `Get-AzureApplicationGateway`. Essa etapa não é necessária.


	Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	


## Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um Application Gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um Application Gateway para usar com o ILB, confira [Criar um Application Gateway com um ILB (Balanceador de Carga Interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO2-->