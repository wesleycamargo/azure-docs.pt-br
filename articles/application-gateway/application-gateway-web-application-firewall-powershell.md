---
title: Configurar o firewall do aplicativo Web - Gateway de Aplicativo do Azure | Microsoft Docs
description: "Este artigo oferece orientação sobre como começar a usar o firewall do aplicativo Web em um Gateway de Aplicativo novo ou existente."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: dab489a1c9fb7d4a51b9ccbce543b209bec23575
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar o firewall do aplicativo Web em um Gateway de Aplicativo novo ou existente

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar um Gateway de Aplicativo habilitado para firewall de aplicativo Web ou adicionar um firewall do aplicativo Web a um Gateway de Aplicativo existente.

O firewall de aplicativo Web (WAF) no Gateway de Aplicativo do Azure protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão.

O Gateway de Aplicativo do Azure é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Gateway de Aplicativo fornece muitos recursos do ADC (controlador de entrega de aplicativos), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de protocolo SSL, as sondas de integridade personalizadas, suporte para vários sites e muitos outros. Para uma lista completa dos recursos com suporte, visite [Visão geral do Gateway de Aplicativo](application-gateway-introduction.md).

O artigo a seguir mostra como [adicionar um firewall do aplicativo Web a um Gateway de Aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway) e como [criar um Gateway de Aplicativo que use o firewall do aplicativo Web](#create-an-application-gateway-with-web-application-firewall).

![imagem de cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração do WAF

Se você tiver lido [Criar um Gateway de Aplicativo com o PowerShell](application-gateway-create-gateway-arm.md), compreende as configurações de SKU a serem definidas durante a criação de um Gateway de Aplicativo. O WAF fornece configurações adicionais para definir ao configurar a SKU em um Gateway de Aplicativo. Não há nenhuma alteração adicional feita no Gateway de Aplicativo em si.

| **Configuração** | **Detalhes**
|---|---|
|**SKU** |Um Gateway de Aplicativo normal sem WAF dá suporte aos tamanhos **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Com a introdução do WAF, há duas SKUs adicionais, **WAF\_Medium** e **WAF\_Large**. Não há suporte para WAF em Gateways de Aplicativo pequenos.|
|**Camada** | Os valores disponíveis são **Standard** ou **WAF**. Ao usar o firewall do aplicativo Web, **WAF** deve ser escolhido.|
|**Modo** | Essa configuração é o modo de WAF. Os valores permitidos são **detecção** e **prevenção**. Quando WAF estiver configurado no modo de detecção, todas as ameaças são armazenadas em um arquivo de log. No modo de prevenção, os eventos ainda estão conectados, mas o invasor recebe uma resposta 403 não autorizado do Gateway de Aplicativo.|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar o firewall do aplicativo Web a um Gateway de Aplicativo existente

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

1. Faça logon na sua Conta do Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Selecione a assinatura a ser usada nesse cenário.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Recupere o gateway ao qual você está adicionando o firewall do aplicativo Web.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

1. Configure a sku do firewall do aplicativo Web. Os tamanhos disponíveis são **WAF\_Large** e **WAF\_Medium**. Quando o firewall do aplicativo Web é usado, a camada deve ser **WAF**. A capacidade deve ser confirmada ao definir o SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

1. Defina as configurações de WAF, conforme definido no exemplo a seguir:

   Para a configuração **FirewallMode**, os valores disponíveis são Prevenção e Detecção.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

1. Atualize o Gateway de Aplicativo com as configurações definidas na etapa anterior.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Este comando atualiza o Gateway de Aplicativo com o firewall do aplicativo Web. Visite [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md) para entender como exibir os logs do seu Gateway de Aplicativo. Devido à natureza de segurança do WAF, será necessário examinar regularmente os logs para compreender a postura de segurança de seus aplicativos Web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um gateway de aplicativo com o firewall do aplicativo Web

As etapas a seguir guiarão você do início ao fim do processo para criar um Gateway de Aplicativo com o firewall do aplicativo Web.

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

1. Faça logon no Azure executando `Login-AzureRmAccount`, será então solicitado que você se autentique com suas credenciais.

1. Verificar as assinaturas da conta executando `Get-AzureRmSubscription`

1. Escolha quais das suas assinaturas do Azure deseja usar.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para o Gateway de Aplicativo.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Gateway de Aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e o local "Oeste dos EUA".

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o Gateway de Aplicativo, veja [Criar um Gateway de Aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.

### <a name="configure-virtual-network"></a>Configurar rede virtual

O Gateway de Aplicativo exige sua própria sub-rede. Nesta etapa, você cria uma rede virtual com um espaço de endereço de 10.0.0.0/16 e duas sub-redes, uma para o Gateway de Aplicativo e outra para os membros do pool de back-end.

```powershell
# Create a subnet configuration object for the Application Gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for Application Gateway instances. With a smaller subnet, you may not be able to add more instance of your Application Gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the backend pool members subnet
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previous created subnets
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-public-ip-address"></a>Configurar o endereço IP público

Para tratar as solicitações externas, o Gateway de Aplicativo requer um endereço IP público. Esse endereço IP público não deve ter um `DomainNameLabel` definido para ser usado pelo Gateway de Aplicativo.

```powershell
# Create a public IP address for use with the Application Gateway. Defining the domainnamelabel during creation is not supported for use with Application Gateway
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurar o Gateway de Aplicativo

```powershell
# Create a IP configuration. This configures what subnet the Application Gateway uses. When Application Gateway starts, it picks up an IP address from the subnet configured and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a backend pool to hold the addresses or NICs for the application that Application Gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authenication certificate that will be used to communicate with the backend servers
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Conifugre the backend HTTP settings to be used to define how traffic is routed to the backend pool. The authenication certificate used in the previous step is added to the backend http settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a frontend port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a frontend IP configuration to associate the public IP address with the Application Gateway
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the Application Gateway. This certificate is used to decrypt and re-encrypt the traffic on the Application Gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the Application Gateway. Assign the front-end ip configuration, port, and ssl certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

#Create a load balancer routing rule that configures the load balancer behavior. In this example, a basic round robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the Application Gateway
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

#Configure the waf configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the Application Gateway utilizing all the previously created configuration objects
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Os Gateways de Aplicativo criados com a configuração básica do firewall do aplicativo Web são configurados com o CRS 3.0 para proteções.

## <a name="get-application-gateway-dns-name"></a>Obter nome DNS do Gateway de Aplicativo

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o Gateway de Aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o Gateway de Aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do Gateway de Aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para configurar um alias, recupere os detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do Gateway de Aplicativo.

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

Saiba como configurar o log de diagnóstico para registrar os eventos detectados ou impedidos pelo firewall do aplicativo Web ao visitar o [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md)

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

