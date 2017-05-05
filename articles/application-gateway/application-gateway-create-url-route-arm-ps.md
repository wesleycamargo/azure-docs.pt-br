---
title: Criar um gateway de aplicativo usando as regras de roteamento de URL | Microsoft Docs
description: "Esta página oferece instruções para criar, configurar um gateway de aplicativo do Azure usando as regras de roteamento de URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 76dfd1c2b2f17e6bc798f4313c4dc4817d2136a4
ms.lasthandoff: 04/04/2017


---
# <a name="create-an-application-gateway-using-path-based-routing"></a>Criar um application gateway usando roteamento com base em caminho

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)

O roteamento com base em caminho de URL permite que você associe rotas com base no caminho de URL de uma solicitação Http. Ele verifica se há uma rota para um pool de back-end configurado para a URL apresentada no Gateway de Aplicativo e envia o tráfego de rede para o pool de back-end definido. Um uso comum para o roteamento com base em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O roteamento com base em URL apresenta um novo tipo de regra ao application gateway. O application gateway tem dois tipos de regra: básica e PathBasedRouting. O tipo de regra básica fornece o serviço de round robin para os pools de back-end, enquanto o PathBasedRouting também leva em consideração, além de distribuição round robin, o padrão de caminho da URL da solicitação ao escolher o pool de back-end.

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
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
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
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
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

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos. Este exemplo cria uma VNET para o Gateway de Aplicativo. O Gateway de Aplicativo requer sua própria sub-rede e é por esse motivo que a sub-rede criada para o Gateway de Aplicativo é menor do que o espaço de endereço da rede virtual. Isso permite outros recursos, incluindo, dentre outras coisas, que servidores Web sejam configurados na mesma rede virtual.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual.  Isso cria o objeto de configuração de sub-rede para o Gateway de Aplicativo que é usado no exemplo a seguir.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24. Isso conclui a configuração da rede virtual com uma única sub-rede em que o Gateway de Aplicativo deve residir.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Etapa 3

Atribua a variável de sub-rede para as próximas etapas, a qual será passada para o cmdlet `New-AzureRMApplicationGateway` em uma etapa futura.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA. O Gateway de Aplicativo pode usar um endereço IP público, interno ou de ambos os tipos para receber solicitações de balanceamento de carga.  Este exemplo usa apenas um endereço IP público. No exemplo a seguir, nenhum nome DNS é configurado para criar o endereço IP público.  O Gateway de Aplicativo não dá suporte a nomes DNS personalizados em endereços IP públicos.  Se um nome personalizado for exigido para o ponto de extremidade público, um registro CNAME deve ser criado a fim de apontar para o nome DNS gerado automaticamente para o endereço IP público.

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

Configure o pool de endereços IP de back-end denominado **pool01** e **pool2** com os endereços IP para o **pool1** e **pool2**. Esses endereços IP são os endereços IP dos recursos que estão hospedando o aplicativo Web que será protegido pelo Gateway de Aplicativo. A integridade desses membros do pool de back-end é validada por investigações, sejam básicas ou personalizadas.  O tráfego é encaminhado para eles quando a solicitação chega no Gateway de Aplicativo. Os pools de back-end podem ser usados por várias regras dentro do Gateway de Aplicativo, o que significa que um pool de back-end pode ser usado para vários aplicativos Web que residem no mesmo host.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no caminho da URL. Um pool recebe o tráfego do caminho de URL "/video", e o outro pool recebe o tráfego do caminho "/image". Substitua os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo. 

### <a name="step-3"></a>Etapa 3

Defina as configurações de **poolsetting01** e **poolsetting02** do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.  As configurações HTTP de back-end são usadas pelas regras para rotear o tráfego para os membros do pool de back-end corretos. Isso determina o protocolo e a porta usados ao enviar tráfego para os membros do pool de back-end. As sessões baseadas em cookies também são determinadas pelas configurações HTTP de back-end.  Se habilitada, a afinidade de sessão baseada em cookies enviará o tráfego para o mesmo back-end das solicitações anteriores para cada pacote.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Etapa 4

Configure o IP front-end com o ponto de extremidade IP público. O objeto de configuração de IP front-end é usado por um ouvinte para relacionar o endereço IP voltado para fora com o ouvinte.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Etapa 5

Configure a porta front-end para um gateway de aplicativo. O objeto de configuração de porta front-end é usado por um ouvinte para definir a porta cujo tráfego deverá ser ouvido pelo Gateway de Aplicativo.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Etapa 6

Crie o ouvinte. Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. O exemplo a seguir usa a configuração de IP de front-end definida anteriormente, uma configuração de porta front-end e um protocolo (http ou https) e configura o ouvinte. Neste exemplo, o ouvinte ouve o tráfego HTTP na porta 80 no endereço IP público que foi criado anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Etapa 7

Configure os caminhos de regra de URL para os pools de back-end. Esta etapa configura o caminho relativo usado pelo application gateway para definir o mapeamento entre o caminho da URL e qual pool de back-end será atribuído para lidar com o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho deve começar com /, sendo que o único lugar no qual um "\*" é permitido é no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro “?” ou “#”, e esses caracteres não são permitidos. 

O exemplo a seguir cria duas regras: uma para o tráfego do caminho de roteamento "/imagem/" para o back-end "pool1" e outra para o tráfego do caminho de roteamento "/video/" para o back-end "pool2". Essas regras garantem que o tráfego para cada conjunto de URLs seja roteado para o back-end. Por exemplo, http://contoso.com/image/figure1.jpg irá para o pool1 e http://contoso.com/video/example.mp4 irá para o pool2.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

A configuração de mapa do caminho de regra também configurará um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas. Por exemplo, http://contoso.com/shoppingcart/test.html irá para o pool1, já que ele está definido como o pool padrão para tráfego sem correspondência.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Etapa 8

Crie uma configuração de regra. Esta etapa configura o application gateway a fim de usar o roteamento de com base no caminho de URL. A variável `$urlPathMap` definida na etapa anterior é usada agora para criar a regra com base no caminho. Nesta etapa, associe a regra com um ouvinte e o mapeamento de caminho de URL criado anteriormente.

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

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para configurar o registro CNAME de IP de front-end, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do Application Gateway.

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


