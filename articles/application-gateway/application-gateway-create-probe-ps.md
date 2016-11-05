---
title: Criar uma investigação personalizada para o gateway de aplicativo usando o PowerShell no Gerenciador de Recursos | Microsoft Docs
description: Saiba como criar uma investigação personalizada para o gateway de aplicativo usando o PowerShell no Gerenciador de Recursos
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: gwallace

---
# Criar uma investigação personalizada para o Azure Application Gateway usando o PowerShell do Gerenciador de Recursos do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[classic deployment model](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### Etapa 1
Use Login-AzureRmAccount para se autenticar.

    Login-AzureRmAccount

### Etapa 2
Verificar as assinaturas da conta.

    Get-AzureRmSubscription

### Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar.<BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Etapa 4
Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Application Gateway usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

## Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
As etapas a seguir criarão uma rede virtual e uma sub-rede para o gateway de aplicativo.

### Etapa 1
Atribua o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Etapa 2
Crie uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Etapa 3
Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

    $subnet = $vnet.Subnets[0]

## Criar um endereço IP público para a configuração de front-end
Crie um recurso de IP público "publicIP01" no grupo de recursos "appgw-rg" para a região Oeste dos EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Criar um objeto de configuração do gateway de aplicativo com uma investigação personalizada
Configure todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### Etapa 1
Crie uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Etapa 2
Configure o pool de endereços IP de back-end denominado "pool01" com os endereços IP "134.170.185.46, 134.170.188.221, 134.170.185.50". Esses valores são os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Etapa 3
A investigação personalizada é configurada nesta etapa.

Os parâmetros usados são:

* **Interval**: configura as verificações de intervalo de investigação em segundos.
* **Timeout**: define o tempo limite da investigação para uma verificação de resposta HTTP.
* **-Hostname e path**: caminho completo da URL invocado pelo Application Gateway para determinar a integridade da instância. Por exemplo, se você tiver um site http://contoso.com/, a investigação personalizada poderá ser configurada para "http://contoso.com/path/custompath.htm" para verificações de investigação com uma resposta HTTP bem-sucedida.
* **UnhealthyThreshold**: o número de respostas HTTP com falha necessárias para sinalizar a instância de back-end como *unhealthy*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### Etapa 4
Define a configuração "poolsetting01" do gateway de aplicativo para o tráfego no pool de back-end. Esta etapa também tem uma configuração de tempo limite para a resposta do pool de back-end a uma solicitação do gateway de aplicativo. Quando uma resposta de back-end atinge um tempo limite, o Application Gateway cancela a solicitação. Esse valor é diferente de um tempo limite de investigação, que é apenas para a resposta de back-end às verificações de investigação.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### Etapa 5
Configure a porta IP de front-end chamada "frontendport01" para o ponto de extremidade de IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Etapa 6
Crie a configuração de IP de front-end chamada "fipconfig01" e associe o endereço IP público à configuração de IP de front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Etapa 7
Crie o ouvinte chamado "listener01" e associe a porta de front-end à configuração de IP de front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Etapa 8
Crie a regra de roteamento do balanceador de carga chamada "rule01", que configura o comportamento do balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Etapa 9
Configure o tamanho da instância do gateway de aplicativo.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


> [!NOTE]
> O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Standard\_Small, Standard\_Medium e Standard\_Large.
> 
> 

## Criar um gateway de aplicativo usando New-AzureRmApplicationGateway
Crie um gateway de aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado de "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Adicionar uma investigação a um Application Gateway existente
Você tem quatro etapas para adicionar uma investigação personalizada a um Application Gateway existente.

### Etapa 1
Carregue o recurso de Application Gateway em uma variável do PowerShell usando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Etapa 2
Adicionar uma investigação à configuração de gateway existente.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


No exemplo, a investigação personalizada é configurada para verificar o caminho de URL contoso.com/path/custompath.htm a cada 30 segundos. Um limite de tempo de 120 segundos é configurado com um número máximo de 8 solicitações de investigação com falha.

### Etapa 3
Adicione a investigação à configuração do pool de back-end e o tempo limite usando **-Set-AzureRmApplicationGatewayBackendHttpSettings**.

     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Etapa 4
Salve a configuração no Application Gateway usando **Set-AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## Remover uma investigação de um Application Gateway existente
Estas são as etapas para remover uma investigação personalizada de um Application Gateway existente.

### Etapa 1
Carregue o recurso de Application Gateway em uma variável do PowerShell usando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Etapa 2
Remova a configuração de investigação do Application Gateway usando **Remove-AzureRmApplicationGatewayProbeConfig**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Etapa 3
Atualize a configuração do pool de back-end para remover a configuração de investigação e tempo limite usando **-Set-AzureRmApplicationGatewayBackendHttpSettings**.

     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Etapa 4
Salve a configuração no Application Gateway usando **Set-AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## Próximas etapas
Saiba como configurar o descarregamento de SSL ao visitar [Configurar descarregamento de SSL](application-gateway-ssl-arm.md)

<!---HONumber=AcomDC_0907_2016-->