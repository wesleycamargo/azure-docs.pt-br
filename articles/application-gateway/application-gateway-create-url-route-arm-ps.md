<properties
   pageTitle="Criar um gateway de aplicativo usando as regras de roteamento de URL | Microsoft Azure"
   description="Esta página oferece instruções para criar, configurar um gateway de aplicativo do Azure usando as regras de roteamento de URL"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# Criar um gateway de aplicativo usando roteamento com base em URL 

O Roteamento com base em Caminho de URL permite que você associe rotas com base no caminho de URL da solicitação Http. Ele verifica se há uma rota para um pool de back-end configurado para as listas de URLs no Application Gateway, e envia o tráfego de rede para o pool de back-end definido. Um uso comum para o roteamento com base em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidor back-end diferentes.

O roteamento com base em URL apresenta um novo tipo de regra ao gateway de aplicativo. O gateway de aplicativo tem dois tipos de regra: básica e PathBasedRouting. O tipo de regra básica fornece o serviço de round robin para os pools de back-end, enquanto o PathBasedRouting também leva em consideração, além de distribuição round robin, o padrão de caminho da URL da solicitação ao escolher o pool de back-end.

>[AZURE.IMPORTANT] PathPattern: a lista de padrões de caminho para correspondência. Cada um deve começar com /, e o único lugar no qual um * é permitido é no final após um “/”. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro? ou #, e esses caracteres não são permitidos.

## Cenário
No exemplo a seguir, o Application Gateway está fornecendo o tráfego para contoso.com com dois pools de servidor back-end: o pool de servidores de vídeo e o pool de servidores de imagem.

As solicitações para http://contoso.com/image* serão roteadas para o pool de servidores de imagem (pool1), e http://contoso.com/video* serão roteadas para o pool de servidores de vídeo (pool2). Um pool de servidores padrão (pool1) será selecionado se nenhum dos padrões de caminho corresponderem.

![roteamento de url](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você criará uma rede virtual e uma sub-rede para o Application Gateway. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores adicionados ao pool de back-end a fim de usar o gateway de aplicativo deve existir ou ter seus pontos de extremidade na rede virtual ou com um IP/VIP público atribuído.



## O que é necessário para criar um gateway de aplicativo?


- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL).
- **Regra:** a regra vincula o ouvinte, o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico.

## Criar um novo gateway de aplicativo

A diferença entre usar o Azure Classic e o Gerenciador de Recursos do Azure é a ordem em que você vai criar o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.


A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo.
3. Criar um objeto de configuração do gateway do aplicativo.
4. Criar um recurso de gateway de aplicativo.

## Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

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

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
Como alternativa, também é possível pode criar marcas para um grupo de recursos para o gateway de aplicativo:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

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

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Etapa 3
Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

	$subnet=$vnet.Subnets[0]

## Criar um endereço IP público para a configuração de front-end
Crie um recurso IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Um endereço IP será atribuído ao gateway de aplicativo quando o serviço for iniciado.

## Criar uma configuração do gateway de aplicativo

Você precisa configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### Etapa 1
Crie uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway for iniciado, ele escolherá um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Lembre-se de que cada instância usará um endereço IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Etapa 2
Configure o pool de endereços IP de back-end denominado "pool01"e "pool2" com os endereços IP "134.170.185.46, 134.170.188.221,134.170.185.50" para "pool1" e "134.170.186.46, 134.170.189.221,134.170.186.50" para o "pool2".


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no caminho da URL. Um pool recebe o tráfego do caminho de URL "/video", e o outro pool recebe o tráfego do caminho "/image". É necessário substituir os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

### Etapa 3
Defina as configurações de "poolsetting01" e "poolsetting02" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Etapa 4
Configure o IP front-end com o ponto de extremidade IP público.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Etapa 5 
Configure a porta front-end para um gateway de aplicativo.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Etapa 6
Crie o ouvinte. Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede.
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Etapa 7 
Configure os caminhos de regra de URL para os pools de back-end. Esta etapa configura o caminho relativo usado pelo gateway de aplicativo para definir o mapeamento entre o caminho da URL e qual pool de back-end será atribuído para lidar com o tráfego de entrada.

O exemplo a seguir cria duas regras: uma para o tráfego do caminho de roteamento "/imagem/" para o back-end "pool1", e outra para o tráfego do caminho de roteamento "/video/" para o back-end "pool2".
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting01

A configuração de mapa do caminho de regra também configura um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas.

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Etapa 8 
Crie uma configuração de regra. Esta etapa configura o gateway de aplicativo a fim de usar o roteamento de com base no caminho de URL.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### Etapa 9 
Configure o número de instâncias e o tamanho do gateway de aplicativo.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Criar um Application Gateway
Crie um gateway de aplicativo com todos os objetos de configuração das etapas acima.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Obter um Application Gateway
	$getgw =  Get-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname

<!---HONumber=AcomDC_0224_2016-->