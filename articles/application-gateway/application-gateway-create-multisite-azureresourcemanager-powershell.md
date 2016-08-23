<properties
   pageTitle="Criar um Application Gateway para hospedar vários sites | Microsoft Azure"
   description="Esta página fornece instruções para criar e configurar um Azure Application Gateway para hospedar vários aplicativos Web no mesmo gateway."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>


# Criar um Application Gateway para hospedar vários aplicativos Web

A hospedagem de vários sites permite que você implante mais de um aplicativo Web no mesmo Application Gateway. Ela depende da presença do cabeçalho de host na solicitação HTTP de entrada, para determinar quais ouvintes devem receber tráfego. O ouvinte, em seguida, direciona o tráfego ao pool de back-end apropriado conforme configurado na definição de regras do gateway. No caso de aplicativos Web habilitados com SSL, o Application Gateway depende da extensão de SNI (Indicação de Nome de Servidor) para escolher o ouvinte correto para o tráfego da Web.

Um uso comum para a hospedagem de vários sites é balancear a carga das solicitações para domínios da Web diferentes para pools de servidor back-end diferentes. Da mesma forma, vários subdomínios do mesmo domínio raiz também podem ser hospedados no mesmo Application Gateway.


## Cenário
No exemplo a seguir, o Application Gateway está fornecendo o tráfego para contoso.com e fabrikam.com com dois pools de servidor back-end: o pool de servidores contoso e o pool de servidores fabrikam. É possível usar uma configuração semelhante para hospedar subdomínios, como app.contoso.com e blog.contoso.com.


## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você criará uma rede virtual e uma sub-rede para o gateway de aplicativo. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores adicionados ao pool de back-end a fim de usar o gateway de aplicativo deve existir ou ter seus pontos de extremidade na rede virtual ou com um IP/VIP público atribuído.



## O que é necessário para criar um gateway de aplicativo?


- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. O FQDN também pode ser usado.
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). Para Application Gateways habilitados para vários sites, os indicadores de SNI e nome do host também são adicionados.
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico.

## Criar um novo gateway de aplicativo

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


		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### Etapa 4
Crie um novo grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-RG -location "East Asia"
Como alternativa, também é possível pode criar marcas para um grupo de recursos para o gateway de aplicativo:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "East Asia" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"} 

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e a localização "East Asia".

>[AZURE.NOTE] Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.

## Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos.

### Etapa 1
Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Etapa 2
Crie uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "East Asia" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Etapa 3
Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -VirtualNetwork $vnet

## Criar um endereço IP público para a configuração de front-end
Crie um recurso IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "East Asia" -AllocationMethod Dynamic

Um endereço IP será atribuído ao gateway de aplicativo quando o serviço for iniciado.

## Criar uma configuração do gateway de aplicativo

Você precisa configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### Etapa 1
Crie uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway for iniciado, ele escolherá um endereço IP na sub-rede configurada e encaminhará o tráfego da rede para os endereços IP no pool de IPs do back-end. Lembre-se de que cada instância usará um endereço IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Etapa 2
Configure o pool de endereços IP de back-end denominado "pool01" e "pool2" com os endereços IP "10.0.0.100, 10.0.0.101,10.0.0.102" para "pool1" e "10.0.0.103, 10.0.0.104, 10.0.0.105" para "pool2".


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.100, 10.0.0.101,10.0.0.102 
	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.0.103, 10.0.0.104, 10.0.0.105

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no site solicitado. Um pool recebe o tráfego do site "contoso.com" e outro pool recebe o tráfego do site "fabrikam.com". É necessário substituir os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo. Observe que em vez de endereços IP internos, você também pode usar endereços IP públicos, FQDN ou NIC da VM para instâncias de back-end. Use o parâmetro "-BackendFQDNs" no PowerShell para especificar FQDNs em vez de IPs.

### Etapa 3
Defina as configurações de "poolsetting01" e "poolsetting02" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.


	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Etapa 4
Configure o IP front-end com o ponto de extremidade IP público.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Etapa 5 
Configure a porta front-end para um gateway de aplicativo.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### Etapa 6
Configure dois certificados SSL para os dois sites para os quais daremos suporte nesse exemplo. Um certificado é para tráfego de contoso.com e o outro é para o tráfego de fabrikam.com. Eles devem ser certificados emitidos por uma Autoridade de Certificação para seus sites. Os certificados autoassinados têm suporte, mas não são recomendados para o tráfego de produção.

	$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name  contosocert -CertificateFile <file path> -Password <password>
	$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### Etapa 7
Configure dois ouvintes para os dois sites neste exemplo. Esta etapa configura os ouvintes para o endereço IP público, a porta e o host usados para receber o tráfego de entrada. O parâmetro HostName é necessário para o suporte a vários sites e deve ser definido para o site apropriado para o qual o tráfego será recebido. O parâmetro RequireServerNameIndication deve ser definido como true para sites que precisam de suporte para SSL no cenário de vários hosts. Se o suporte a SSL for necessário, você também precisará especificar o certificado SSL que será usado para proteger o tráfego para aquele aplicativo Web. A combinação de FrontendIPConfiguration, FrontendPort e HostName deve ser exclusiva para um ouvinte. Cada ouvinte pode dar suporte a um certificado.
 
	$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
	$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02


### Etapa 8 
Crie duas configurações de regra para os dois aplicativos Web nesse exemplo. Uma regra vincula ouvintes, pools de back-end e as configurações de http. Esta etapa configura o Application Gateway para usar a regra de roteamento Básica, uma para cada site. O tráfego para cada site é recebido pelo seu ouvinte configurado e é direcionado para seu pool de back-end configurado, usando as propriedades especificadas no BackendHttpSettings.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
	$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### Etapa 9 
Configure o número de instâncias e o tamanho do gateway de aplicativo.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## Criar um Application Gateway
Crie um gateway de aplicativo com todos os objetos de configuração das etapas acima.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "East Asia" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02 	


>[AZURE.IMPORTANT] O provisionamento do Application Gateway é uma operação demorada e pode levar algum tempo para ser concluída.

## Obter um nome DNS de Application Gateway
Recupere detalhes do Application Gateway e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Application Gateway. O nome DNS do Application Gateway deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do Application Gateway.
	
	Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01
		
	Name                     : publicIP01
	ResourceGroupName        : appgw-RG
	Location                 : eastasia
	Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
	ResourceGuid             : 00000000-0000-0000-0000-000000000000
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : xx.xx.xxx.xx
	PublicIpAddressVersion   : IPv4
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
	                           Configurations/frontend1"
	                           }
	DnsSettings              : {
	                             "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
	                           }

<!---HONumber=AcomDC_0810_2016-->