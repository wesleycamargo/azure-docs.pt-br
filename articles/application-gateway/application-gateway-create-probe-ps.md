---
title: "Criar uma investigação personalizada para o gateway de aplicativo usando o PowerShell no Resource Manager | Microsoft Docs"
description: "Saiba como criar uma investigação personalizada para o gateway de aplicativo usando o PowerShell no Gerenciador de Recursos"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 09aeb63d4c2e68f22ec02f8c08f5a30c32d879dc
ms.openlocfilehash: d6856256a9ab272e0620e82d140c5ec735e5eabc


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Criar uma investigação personalizada para o Azure Application Gateway usando o PowerShell do Gerenciador de Recursos do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Etapa 1

Use `Login-AzureRmAccount` para autenticar.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Application Gateway usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado **appgw-RG** e a localização **Oeste dos EUA**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

As etapas a seguir criarão uma rede virtual e uma sub-rede para o gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Etapa 3

Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Criar um objeto de configuração do gateway de aplicativo com uma investigação personalizada

Configure todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Application Gateway é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Etapa 2

Configure o pool de endereços IP de back-end denominado **pool01** com os endereços IP **134.170.185.46, 134.170.188.221, 134.170.185.50**. Esses valores são os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP acima para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>Etapa 3

A investigação personalizada é configurada nesta etapa.

Os parâmetros usados são:

* **Interval** : configura as verificações de intervalo de investigação em segundos.
* **Timeout** : define o tempo limite da investigação para uma verificação de resposta HTTP.
* **Hostname e path** - caminho completo da URL invocado pelo Gateway de Aplicativo para determinar a integridade da instância. Por exemplo, se você tiver um site **http://contoso.com/**, a investigação personalizada poderá ser configurada para **http://contoso.com/path/custompath.htm** para verificações de investigação com uma resposta HTTP bem-sucedida.
* **UnhealthyThreshold** : o número de respostas HTTP com falha necessárias para sinalizar a instância de back-end como **unhealthy**.

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>Etapa 4

Defina a configuração **poolsetting01** do gateway de aplicativo para o tráfego no pool de back-end. Esta etapa também tem uma configuração de tempo limite para a resposta do pool de back-end a uma solicitação do gateway de aplicativo. Quando uma resposta de back-end atinge um tempo limite, o Application Gateway cancela a solicitação. Esse valor é diferente de um tempo limite de investigação, que é apenas para a resposta de back-end às verificações de investigação.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>Etapa 5

Configure a porta IP de front-end chamada **frontendport01** para o ponto de extremidade de IP público.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>Etapa 6

Crie a configuração de IP de front-end chamada **fipconfig01** e associe o endereço IP público à configuração de IP de front-end.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>Etapa 7

Crie o ouvinte chamado **listener01** e associe a porta de front-end à configuração de IP de front-end.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>Etapa 8

Crie a regra de roteamento do balanceador de carga chamada **rule01**, que configura o comportamento do balanceador de carga.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>Etapa 9

Configure o tamanho da instância do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> O valor padrão para **InstanceCount** é 2, com um valor máximo de 10. O valor padrão para **GatewaySize** é Medium. Você pode escolher entre **Standard_Small**, **Standard_Medium** e **Standard_Large**. 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Criar um gateway de aplicativo usando New-AzureRmApplicationGateway

Crie um gateway de aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado de **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Adicionar uma investigação a um Application Gateway existente

Você tem quatro etapas para adicionar uma investigação personalizada a um Application Gateway existente.

### <a name="step-1"></a>Etapa 1

Carregue o recurso do gateway de aplicativo em uma variável do PowerShell usando `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Etapa 2

Adicionar uma investigação à configuração de gateway existente.

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

No exemplo, a investigação personalizada é configurada para verificar o caminho de URL contoso.com/path/custompath.htm a cada 30 segundos. Um limite de tempo de 120 segundos é configurado com um número máximo de 8 solicitações de investigação com falha.

### <a name="step-3"></a>Etapa 3

Adicione a investigação à configuração do pool de back-end e o tempo limite usando `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>Etapa 4

Salve a configuração no gateway de aplicativo usando `Set-AzureRmApplicationGateway`.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Remover uma investigação de um Application Gateway existente

Estas são as etapas para remover uma investigação personalizada de um Application Gateway existente.

### <a name="step-1"></a>Etapa 1

Carregue o recurso do gateway de aplicativo em uma variável do PowerShell usando `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Etapa 2

Remova a configuração de investigação do gateway de aplicativo usando `Remove-AzureRmApplicationGatewayProbeConfig`.

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>Etapa 3

Atualize a configuração do pool de back-end para remover a investigação e o tempo limite usando `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Etapa 4

Salve a configuração no gateway de aplicativo usando `Set-AzureRmApplicationGateway`. 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
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

Saiba como configurar o descarregamento de SSL ao visitar [Configurar descarregamento de SSL](application-gateway-ssl-arm.md)




<!--HONumber=Dec16_HO3-->


