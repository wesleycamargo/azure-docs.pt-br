---
title: Criar um gateway de aplicativo usando as regras de roteamento de URL | Microsoft Docs
description: "Esta página oferece instruções para criar, configurar um gateway de aplicativo do Azure usando as regras de roteamento de URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: jdial
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: 9af41bac2f073e5d3770ac17357306e1af86c3e6


---
# <a name="create-an-application-gateway-using-path-based-routing"></a>Criar um application gateway usando roteamento com base em caminho

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> 
> 

O Roteamento com base em caminho de URL permite que você associe rotas com base no caminho de URL da solicitação Http. Ele verifica se há uma rota para um pool de back-end configurado para as listas de URLs no Application Gateway, e envia o tráfego de rede para o pool de back-end definido. Um uso comum para o roteamento com base em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O roteamento com base em URL apresenta um novo tipo de regra ao application gateway. O application gateway tem dois tipos de regra: básica e PathBasedRouting. O tipo de regra básica fornece o serviço de round robin para os pools de back-end, enquanto o PathBasedRouting também leva em consideração, além de distribuição round robin, o padrão de caminho da URL da solicitação ao escolher o pool de back-end.

> [!IMPORTANT]
> PathPattern: a lista de padrões de caminho para correspondência. Cada um deve começar com /, e o único lugar no qual um "\*" é permitido é no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro “?” ou “#”, e esses caracteres não são permitidos. 
> 
> 

## <a name="scenario"></a>Cenário

No exemplo a seguir, o Application Gateway está fornecendo o tráfego para contoso.com com dois pools de servidor back-end: o pool de servidores de vídeo e o pool de servidores de imagem.

As solicitações de http://contoso.com/image* são roteadas para o pool de servidores de imagem (pool1) e as de http://contoso.com/video* são roteadas para o pool de servidores de vídeo (pool2). Um pool de servidores padrão (pool1) será selecionado se nenhum dos padrões de caminho corresponderem.

![roteamento de url](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você cria uma rede virtual e uma sub-rede para o Application Gateway. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores adicionados ao pool de back-end a fim de usar o gateway de aplicativo deve existir ou ter seus pontos de extremidade na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

* **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico.

## <a name="create-an-application-gateway"></a>Criar um Application Gateway

A diferença entre usar o Azure Classic e o Azure Resource Manager é a ordem em que você cria o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.

A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo.
3. Criar um objeto de configuração do gateway do aplicativo.
4. Criar um recurso de gateway de aplicativo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Fazer logon no Azure

```powershell
Login-AzureRmAccount
```

Você deve se autenticar com suas credenciais.<BR>

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Como alternativa, também é possível pode criar marcas para um grupo de recursos para o gateway de aplicativo:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Application Gateway usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Etapa 3

Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Um endereço IP é atribuído ao application gateway quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar uma configuração do gateway de aplicativo

Todos os itens de configuração devem estar configurados antes de criar o application gateway. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Etapa 2

Configure o pool de endereços IP de back-end denominado **pool01** e **pool2** com os endereços IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** para o **pool1** e **134.170.186.46**, **134.170.189.221**, **134.170.186.50** para o **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50
```

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no caminho da URL. Um pool recebe o tráfego do caminho de URL "/video", e o outro pool recebe o tráfego do caminho "/image". Substitua os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo. 

### <a name="step-3"></a>Etapa 3

Defina as configurações de **poolsetting01** e **poolsetting02** do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Etapa 4

Configure o IP front-end com o ponto de extremidade IP público.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Etapa 5

Configure a porta front-end para um gateway de aplicativo.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Etapa 6

Crie o ouvinte. Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. 

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Etapa 7

Configure os caminhos de regra de URL para os pools de back-end. Esta etapa configura o caminho relativo usado pelo application gateway para definir o mapeamento entre o caminho da URL e qual pool de back-end será atribuído para lidar com o tráfego de entrada.

O exemplo a seguir cria duas regras: uma para o tráfego do caminho de roteamento "/imagem/" para o back-end "pool1", e outra para o tráfego do caminho de roteamento "/video/" para o back-end "pool2".

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

A configuração de mapa do caminho de regra também configura um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas. 

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Etapa 8

Crie uma configuração de regra. Esta etapa configura o application gateway a fim de usar o roteamento de com base no caminho de URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Etapa 9

Configure o número de instâncias e o tamanho do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Criar um Application Gateway

Crie um application gateway com todos os objetos de configuração das etapas anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Obter um nome DNS de Application Gateway

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para isso, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
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
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser aprender sobre o descarregamento de protocolo SSL, consulte [Configurar um Application Gateway para o descarregamento SSL](application-gateway-ssl-arm.md).




<!--HONumber=Nov16_HO3-->


